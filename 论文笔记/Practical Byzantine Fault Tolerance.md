> 拜占庭容错算法论文笔记。
>
> PBFT是联盟链共识算法的基础。实现了在有限个节点的情况下的拜占庭问题，有3f+1的容错性，并同时保证一定的性能。

# Introduction

PBFT算法起作用的前提条件是n个副本中最多有![image-20201118122650138](Practical%20Byzantine%20Fault%20Tolerance.assets/image-20201118122650138-1605923484476.png)同时出错。且该系统是在异步系统中工作。

## 本论文的三大贡献

* It describes the first state-machine replication protocol that correctly survives Byzantine faults in asynchronous networks.它描述了第一个在异步网络中正确地在拜占庭式错误中生存的状态机复制协议。
* It describes a number of important optimizations that allow the algorithm to perform well so that it can be used in real systems.它描述了许多重要的优化，使算法能够很好地执行，从而可以在实际系统中使用。
* It describes the implementation of a Byzantine-fault-tolerant distributed file system它描述了拜占庭容错分布式文件系统的实现
* It provides experimental results that quantify the cost of the replication technique.它提供了实验结果，量化了复制技术的成本。

# Background

拜占庭将军问题：拜占庭位于如今的土耳其的伊斯坦布尔，是东罗马帝国的首都。

* 由于当时拜占庭罗马帝国国土辽阔，为了达到防御目的，每个军队都分隔很远，将军与将军之间只能靠<u>信差</u>传消息。
* 在战争的时候，拜占庭军队内所有<u>将军和副官必须达成一致的共识</u>，决定是否有赢的机会才去攻打敌人的阵营。
* 但是，在军队内有可能存有<u>叛徒和敌军的间谍</u>，左右将军们的决定又扰乱整体军队的秩序。在进行共识时，结果并不代表大多数人的意见。
* 这时候，在已知有成员谋反的情况下，其余忠诚的将军在不受叛徒的影响下如何达成一致的协议，拜占庭问题就此形成

“拜占庭将军问题”延伸到互联网生活中来，其内涵可概括为：

* 在互联网大背景下，当需要与不熟悉的对方进行<u>价值交换</u>活动时，人们如何才能防止不会被其中的恶意破坏者欺骗、迷惑从而作出错误的决策。
* 进一步将“拜占庭将军问题”延伸到技术领域中来，其内涵可概括为：在缺少可信任的中央节点和可信任的通道的情况下



# System Model  

Our messages contain 

* public-key signatures （公钥签名）, 
* message authentication codes , 
* and message digests（信息摘要） produced by collision-resistant hash functions （由抗碰撞的哈希函数生成）

流程：

* sign the digest(摘要) 
* and append it to the plaintext rather than signing the full msg. 

All replicas know the others’ public keys to verify signatures.

假设对手在计算上有一定的界限，如无法生成非错误结点的有效签名、从摘要中计算摘要汇总的信息，找到两个具有相同摘要的消息

## 确定主节点primary

在算法开始阶段，`主节点` 由 `p = v mod n` 计算得出，随着 `v` 的增长可以看到 `p` 不断变化，论文里目前还是**轮流坐庄(round-robin)**的方法，这里是一个优化点。

# Three phases protocol

## Pre-Prepare Phase

首先是由客户端发送指令，即国王发送命令。正如你所看到的结构体所示，Request里包含了命令，时间戳，客户端的id以及其独一无二的签名。

服务器端接受request时需要签名验证，因为PBFT应用的是联盟链，而不是私链，所以要对操作者的身份进行校验，比如A发起一笔转账，则服务端需要核对是不是A发起的转账，防止盗刷。

然后是primary（主将）接受请求，向其余节点广播Pre-Prepare消息，消息中包含v, n, d, sig, n。<u>值得注意的是，主节点有权分配msg所对应的序号</u>，这些序号就是客户端发送来的消息的执行顺序。

`主节点` 由 `p = v mod n` 计算得出，随着 `v` 的增长可以看到 `p` 不断变化，论文里目前还是**轮流坐庄**的方法，这里是一个优化点。

另外，d其实使用的是collision resistant的hash表，

为什么让primary来决定顺序呢？  如果不是让primary来决定，则需要制定一个agreement protocol，在这个协议中我们会选择这个顺序以及如何投票表决，代价太昂贵了. 

接着就是backups来决定是否接受该msg了，什么时候不接受呢？一种典型的情况就是如果当前pre-prepare消息里的v和n在之前收到的消息里曾经出现过，

* 但是d和m不一样
* 或者序号n不在高低水位之间

此时，就会拒绝请求。至于原因后续我会讲到。

## Prepare Phase

如果副本节点接受预准备消息，就进入了准备阶段。在准备阶段，每一个节点都向其他节点发送<u>包含自己 ID</u> 的准备消息，同时也接收其他节点的准备消息。对于收到准备消息同样进行合法性检查。验证通过则把这个准备消息写入自己的消息日志中。一个节点集齐至少 2f+1 个验证过的消息才进入准备状态。

如果backups接受了主节点的请求，就会向所有其他的结点发送prepare msg，当每个结点收到2f的prepare msg时，进入已准备状态，此时要注意的是2f中包含一个来自自己的prepare msg（这个地方当时困扰了我好久，我甚至去看论文原作者的报告去了）
还有包含一个client Request。

节点收到prepare消息时，会验签并检查是否是当前view的消息，同时检查消息序号n在当前的接收水线内，验证通过则接受该消息，保存到本地log中。

当节点达成以下3点时，则表明节点达成了prepared状态，记为**prepared(m,v,n,i)**。

1. 在log中存在消息m
2. 在log中存在m的pre-prepare消息，pre-prepare(m,v,n)
3. 在log中存在2f个来自其他节点的prepare消息，prepare(m,v,n,i)

至此，可以确保在view不发生切换的情况下，对于消息m有[全局一致]()的顺序。

也就是说，在view不变的情况下:

- (1) 一个正常节点i，不能对两个及以上的不同消息，达成相同序号n的prepared状态。即不能同时存在prepared(m,v,n,i)和prepared(m',v,n,i)
- (2) 两个正常节点i、j，必须对相同的消息m，达成相同序号n的prepared状态。prepared(m,v,n,i) && prepared(m,v,n,j)



## Commit Phase

在提交阶段，每个节点广播 commit 消息告诉其他节点自己已经进入**准备状态**。如果集齐至少 2f+1 的 commit 消息则说明提案通过=》说明已经得到正确的信息，（因为2f+1中至少有f+1个正确结点发出的信息，所以正确）。

因为要确保正确结点间的行为都一致；当收到**2f+1**个有效的commit msg时，就进入committed-local状态，然后开始<u>执行客户端的命令</u>，执行完毕，就向客户端进行回复。

## Reply

达成commit-local之后，节点对于消息m就有了一个全局一致的顺序，可以执行该消息并 reply to 客户端了。

如果客户端没有及时收到回复，它会将request广播给所有节点。

* 若request已经被处理，节点只需重新发送reply给客户端即可。

* 若request没有被处理，非primary的节点就会转播该request给primary。如果此时primary还是没有广播请求给所有其它节点，它就会被怀疑是<u>错误结点</u>，然后进行视图切换 (view change)。



# Service Properties

> BFT类共识算法确保一致性需要保证安全性和活性，

## Safety 安全性

> 安全性指：坏的事情不会发生，即共识系统不能产生错误的结果，比如一部分节点说yes，另一部分说no。在区块链的语义下，指的是不会分叉。程序永远不会产生错误的结果，如「数组下标越界」

The algorithm provides both safety and liveness（安全型与活性） assuming no more than ![image-20201118133500979](Practical%20Byzantine%20Fault%20Tolerance.assets/image-20201118133500979-1605923484477.png) replicas are faulty.

> 安全性要求错误副本的数量要有上限，是因为错误副本可以任意地行动，如摧毁自身的状态

所有由有缺陷的客户端执行的操作都会被无缺陷的客户端以一致的方式观察到。特别是，如果服务操作被设计为保留服务状态上的一些不变量，那么有问题的客户端就不能破坏这些不变量。

### 新旧view中的消息序号一致

现在考虑存在视图变更的情况。首先，在视图 v 中，对于任意一个请求 m 来说，如果其在某个正常副本节点 i 完成了本地确认(committed local)，假设其消息序号为 n 。
那么，就有 committed(m, v, n) 为 true 。 这也意味着存在一个节点集合R1, 其中至少含有 f+1 个正常副本节点，并且对于其中每一个节点 i ，prepared(m, v, n, i) 为 true。

然后，考虑系统最终变更视图到 v' 的情况。在从视图 v 变更到 v' 的过程中，此时变更没有完成，正常副本节点在接收到 new-view 消息之前，不会接受视图 v' 上的预准备(pre-prepared)消息。

但是，对于视图 v 变更到 v'的任意一个有效的 new-view 消息来说，其中包含 <u>2f+1</u> 个有效的view-change 消息。这些消息来自不同的副本节点，记它们组成的集合为**R2**。**R1**和**R2**至少有一个相同的元素，也就是相同的节点。不然的话，它们总的节点个数将为(f+1) + (2f+1) = 3f+2，这与我们假定的系统总节点个数 3f+1 不符。

因此，假设这个共同的正常副本节点为 k 。对于请求 m 来说，只有可能存在下面两种情况：

1. new-view 消息中，存在 view-change 消息，其中包含的最新stable checkpoint对应的消息序号大于 m 的序号 n 。
2. new-view 消息中所有的 view-change 消息中包含的最新stable checkpoint对应的消息序号不大于m的序号n。

对于第一种情况，根据视图变更协议，新视图 v' 中，所有正常副本节点不会接受序号为n或小于n的消息。

对于第二种情况，m 将会被传播到新视图 v'中，因为根据视图变更协议，min-s≤n。视图变更完成后，在 v' 中，算法将针对编号为 n 的请求 m 重新运行3 phases协议。这就使得所有正常副本节点会达成一致，而不会出现下面这种情况：

另一个不同于 m 的请求 m' ， 其在视图 v 中分配的序号为 n ，且在新视图中 v' 完成本地确认。

综合上面 1 和 2 的证明，就证明了算法在同一视图和视图变更过程中，都会保证本地确认的请求的序号在所有正常副本节点中会达成一致，从而保证了算法的安全性（safety）。



## Liveness活动性

> 指：好的事情一定会发生，即系统一直有回应，在区块链的语义下，指的是共识会持续进行，不会卡住。假如一个区块链系统的共识卡在了某个高度，那么新的交易是没有回应的，也就是不满足liveness。
>
> 程序总会产生预期的结果，如「请求资源一定有返回」。

The algorithm does not rely on synchrony to provide safety. Therefore, it must rely on **synchrony to provide liveness**; otherwise it could be used to implement consensus in an asynchronous system, 该算法不依赖同步来提供安全性。因此，它必须依靠同步来提供活力；否则，它可以用来在一个异步系统中实现一致， 这是不太可能的

### 原文翻译

为了提供活动性，如果副本不能执行一个请求，那么他必须移到一个新的视野(view)中。但是当至少有2f+1个正常副本在同一个视图中时，这种状态尽可能长时间地保持。每次视图变更时，上述时间间隔需要快速增长，比如以指数形式增长。我们通过三个方法实现该目的。

1. 为了防止视图变更太快开始，当一个节点发送 view-change 消息后，在等待接收 2f+1 个 view-change消息时，会同时启动定时器，其超时时间设置为 T。如果视图变更没有在 T 时间内完成，或者在新视图中请求没有在该时间间隔内完成，则会触发新的视图变更。此时，算法会调整超时时间，将其设置为原来的两倍，即 2T 。
2. 除了上述的定时器超时触发节点发送 view-change 消息外，当其接收到来自 f+1 个不同节点的有效view-change 消息，并且变更的目标视图大于节点当前视图时，也会触发节点发送 view-change 消息。这可以防止节点过晚启动视图变更。
3. 基于上述第二点的规则，失效节点无法通过故意发送 view-change 消息来触发频繁的视图变更从而干扰系统的运行。因为失效节点最多只能发送 f 条消息，达不到 f+1 的触发条件。失效节点是主节点时，可能会触发视图变更，但是连续的视图变更最多只会是 f 个，之后主节点就是正常节点。因此，基于以上的规则，系统可以保证活性。

# Checkpoint protocol

> 从本地日志中删除历史消息的机制

对算法来说，为了保证安全性，每个副本节点需要保证如下两点：

1. 对于每个请求来说，在被至少f+1个正常节点执行之前，所有相关的消息都必须记录在消息日志中；
2. 同时，如果一条请求被执行，节点能够在**视图变更**中向其他节点证明这一点。

此外，如果某个副本节点缺失的一些消息正好已经被所有正常节点删除，则需要通过传输部分或全部的服务状态(service state)来使节点状态更新到最新。因此，节点需要某种方式来证明状态(state)的正确性。

如果每执行一次操作，都生成一个状态证明，代价将会很大。因此可以每执行<u>一定数量的请求</u>后生成一次状态证明，例如:只要节点**序号**是某个值比如100的整数倍时，就生成一次，此时称这个状态为**检查点(checkpoint)**。如果一个检查点带有相应的证明，我们则称其为**稳定的检查点**。

### 稳定检查点的生成

如上所述，一个带有证明的检查点被称为stable checkpoint，这种证明的生成过程如下：

1. 当replica i 生成一个checkpoint之后，会组装检查点消息，并全网广播给其他所有replicas，检查点消息格式如下：
   <CHECKPOINT, n, d, i>，
   这里n指的是生成<u>的最后一条**执行**请求的序号</u>，d是当前服务状态的摘要。

2. 每个副本节点等待并收集 2f+1 个来自其它副本节点的checkpoint msg（有可能包括自己的），这些消息有相同的序号 n 和摘要 d。这 2f+1 个msg就是该checkpoint的正确性证明（此时变为stable）。

<u>一旦一个检查点变为stable后，节点将从本地消息日志中**删除**所有序号小于或等于n的请求所对应的预准备、准备和确认消息。</u>同时，会删除所有更早的检查点和对应的检查点消息。

检查点的生成协议可以用来移动低水线 h 和高水线 H：

* h的值就是最新稳定检查点所对应的稳定消息序号；
* 高水线 H = h+k，这里k要设置足够大，至少要大于检查点的生成周期，比如说：假如每隔100条请求生成检查点，k就可以取200。

# View Change

当primary失效时，视图转换（view change）可以让系统继续进行，从而保证了活性（liveness）。View change由超时触发，防止backup无限等待执行请求。backup在接收到请求时会启动计时器（timer），当它不再等待执行请求时会关闭计时器。

## 视图转换过程

如果backup i的计时器在视图v中到达一个阈值，这个backup就会引发一个视图转换，使得整个系统转移到视图v+1。此时，backup i停止接收消息（除了检查点消息、视图转换消息和新视图消息），并将视图转换消息**<VIEW_CHANGE,v+1,n,C,P,i>**广播给所有replica，n是i所知的上一个稳定检查点s的序列号，C是一组2f+1个检查点消息，为s的正确性提供证明，P是一组P_m 的集合，m是序列号大于n并且在replica i准备的请求， P_m 包含一个有效的预准备消息和2f个来自不同backup的有效的准备消息，这些准备消息具有相同的v、n、d并且和预准备消息匹配。

当视图v+1的主节点p接收到**2f**（还有一个是自身的，所以总数为2f+1）个有效的视图转换消息，会广播新视图消息**<NEW_VIEW,v+1,V,Q>**给所有其他replica，

> 为什么是**2f**？原因是，此时不确定view-change是不是由恶意节点发的，所以需要f+1>f个消息才能确定

+ V: 是view-change消息集合

+ O: pre-prepare消息的集合， O按照如下的过程计算：

  + 根据收到的VIEW-CHANGE和PREPARE msg，找出min_s和max_s

    * 集合V中的latest stable checkpoint对应的序号作为min-s，
    * 集合V中的prepare message消息对应最大的序号(由于是异步的，可能有的节点完成的快)作为max-s.

  + 主节点对每一个介于 min-s和max-s之间的request，在v+1(也就是新的视图下)，创建一个**新**的pre-prepare（预准备）消息。（因为易主了，需要重新发送pre-prepare消息）
    这分两种情况：

    (1) 在V中存在一个或多个view-change消息，他们的P集合中某个Pm集合包含了对应序号为n的prepare消息； 

    * 对于第一种情况，主节点创建新的预准备消息，<PRE-PREPARE, v+1, n, d>。d是V中具有highest view number的序列号为n对应的pre-prepare信息中的请求摘要

    (2) 如果在某个序号上从未进行过view change（即第一轮就达成了共识），则PRE-PREPARE中包含一个特殊的null请求的摘要信息。） 

    + 对于第二种情况，创建新的<PRE-PREPARE, v+1, n, d_null>。d_null 是一个特殊的空请求的摘要，它的执行是no-op（no operation）

> 可以这样理解，在新的view中，节点是在上一轮view中各个节点的prepared状态基础上进行共识流程的。
>
> 发生view转换时，需要保证的是：如果视图转换之前的消息m被分配了序号n, 并且达到了prepared状态，那么在视图转换之后，该消息也必须被分配序号n(safety特性)。
>
> * 因为达到prepared状态以后，就有可能存在某个节点committed-local。要保证对于request m committed-local在视图转换之后，其他节点的commit-local依然是一样的序号。
> * 特别的，为了减少重复验证，如果在某个sequence number上从未进行过view change（即<u>第一轮就达成了共识</u>），则PRE-PREPARE中包含一个特殊的**null**请求的摘要信息。



接下来主节点primary将计算后的O的消息写入日志。如果min_s比主节点最近的稳定检查点的序列号n更大，则将具有序列号min_s的检查点的正确性证明插入日志（代替之前的稳定检查点），并且将之前的日志信息丢弃。最后primary进入视图v+1，并可以接收client传递给视图v+1的消息



# 思考

* 该算法没有解决容错隐私问题：一个错误的副本可能会泄露信息给攻击者。在一般情况下，提供容错隐私是不可行的，因为服务操作可能使用其参数和服务状态进行任意计算；需要清除这些信息以有效地执行这些操作。即使存在对服务操作不透明的参数和状态部分的恶意复制阈值（临界值），也可以使用秘密共享方案来获得隐私。
* PBFT的核心设计是Viewchange，巧妙的在Viewchange消息添加prepared信息，实现将previous视图信息传递到下一轮。但是，这样存在的问题是，消息太大，**有些冗余**。



# Q&A

**为什么 `PBFT` 算法只能容忍 `(n-1)/3` 个作恶节点？**

节点总数是 `n`，其中作恶节点有 `f`，那么剩下的正确节点为 `n - f`，意味着只要收到 `n - f` 个消息就能做出决定，但是这 `n - f` 个消息有可能由 `f` 个是由作恶节点冒充的，那么正确的消息就是 `n - f - f` 个，为了多数一致，正确消息必须占多数，也就是 `n - f - f > f`，但是节点必须是整数个，所以 n 最少是 `3f+1` 个。



**为什么需要commit阶段？**

 **Prepared是一个局部视角，不是全局一致**，即副本i看到了非拜占庭节点认可了`<m, v, n>`，但整个系统包含3f+1个节点，异步的系统中，存在<u>丢包、延时、拜占庭节点故意向部分节点发送Prepare等拜占庭行为</u>，副本i**无法确定，其他副本也达到Prepared状态。如果少于f个副本成为Prepared状态，然后执行了请求m，系统就出现了不一致。**