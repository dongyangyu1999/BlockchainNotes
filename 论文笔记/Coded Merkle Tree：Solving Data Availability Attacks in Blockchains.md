Coded Merkle Tree: Solving Data Availability Attacks in Blockchains

> 编码Merkle树:解决区块链的数据可用性攻击

# 1. Introduction

为了更好的扩展性，有必要去研究light nodes（轻量节点：只需要验证特定的交易而不是需要验证所有交易）

比特币中，轻节点主要是通过以下两点实现的。

* 简化支付验证（simplified payment verification，SPV）实现的：Merkle Tree

* 依据账本状态确认交易，如根据交易所在区块在链上的深度，区块在链上越“深”，轻节点对交易的有效性就越有信心

因此，需要做的研究有：

* 轻节点被连接到大部分节点都不是诚实节点的情况，
* 轻节点处如何实现更快的确认

总的想法是设计新的块结构，允许整个节点生成和广播**单个**交易的**简明欺诈证据**（succinct fraud proofs）。这样，轻节点将能够及时验证欺诈交易和阻止，只要它连接到至少一个诚实的完整节点。

在[^9]中提出了一种在执行交易子集后，**利用中间状态Merkle树的根**的有效构造。但是，它容易受到[^9]中描述的所谓“<u>数据可用性攻击</u>”，为此[^9]提出了一种基于纠删码的解决方案。形式化描述数据可用性攻击并全面解决它是本文的主要目的。

**数据可用性攻击**：一个恶意块生产者

1. 发布一个区块头，以便轻节点可以检查所包含的交易
2. 但他会保留区块的一部分（比如无效的交易），使得诚实的完整节点不可能验证区块并且生成欺诈证明（fraud proof）

虽然诚实的完整节点知道数据不可用，但没有好的方法来证明这一点。他们能做的最好的事情就是在没有证据的情况下发出警报。然而，这是有问题的，因为恶意块生产者在听到警报后可以释放隐藏的部分。由于网络延迟，其他节点可能会在收到警报之前收到丢失的部分，因此无法区分是谁在搪塞。因此，没有奖励和惩罚机制可以适当地奖励诚实的完整节点，同时阻止虚假警报和拒绝服务攻击。

因此，为了使欺诈证据起作用，轻节点必须自己确定数据可用性。这导致了以下关键问题:当轻节点接收到某个块的报头时，它如何通过下载该块最少的可能部分来验证该块的内容对网络可用？

**需要对区块进行编码**。由于交易比块小得多，恶意块生产者只需要隐藏块的很小一部分。除非下载整个块，否则轻节点很难检测到这种隐藏。然而，通过适当的纠删码给数据增加冗余，在原始区块上任何小的隐藏将会等同于使编码块的重要部分不可用，这可以由轻节点通过以指数增加的概率随机采样编码区块来检测。作为一种应对措施，恶意的块生产者可以不正确地进行编码，以阻止正确的解码。轻节点依靠诚实的全节点来检测这种攻击，并通过不正确的编码证明来证明。



[^9]: Al-Bassam, M., Sonnino, A., Buterin, V.: Fraud and data availability proofs: Maximising light client security and scaling blockchains with dishonest majorities. eprint arXiv:1809.09044 (2018)

# 2. Security Model





# 3. Overview of Erasure Coding Assisted Approach



# 4. Detailed Description of SPAR

# 5. Performance Analysis

# 6. Implementation for Bitcoin and Experiments



# 7. Conclusion and Discussions

通过迭代地将一组特殊的LDPC码应用到一个Merkle树的每一层，并将每个编码层的散列分批到下一层的数据符号中，我们发明了一种新的**哈希累加器**，称为编码Merkle树(CMT)。在CMT的基础上，我们提出了一种新的数据可用性验证系统，称为SPAR，它允许以恒定的成本检查整个树的可用性和完整性。

SPAR在扩展包含轻型节点的区块链系统方面发挥着关键作用，因为它使这些节点能够以小而恒定的成本实时验证数据可用性和完整性。SPAR还可用于扩展分片区块链系统的通信，其中一个分片的完整节点作为其他分片的轻节点运行，因为SPAR允许它们有效地检查其他分片中块的可用性和完整性。

将SPAR集成到现有的区块链系统中只需要最小的改动，不需要额外的带宽消耗。一个诚实的块生产者只需要像往常一样广播原始数据块，并在块标题中附加CMT根。这足以让其他完整节点重现CMT并为轻节点提供采样服务。我们在Rust的CMT图书馆为平价比特币客户维护着与标准Merkle树模块相同的应用编程接口。注意到经典的Merkle树确实是特殊的CMT，编码率r  = 1，批处理因子q = 2，我们的库很容易替换标准模块，并且向后兼容













# 概念解释

## light vs full nodes

light nodes only interested in verifying some specific transactions. 

在比特币中，轻节点使用简化支付验证(SPV)技术实现：将交易作为叶节点，为每个块构建一个Merkle树，Merkle根存储在块头中。利用Merkle根，轻节点可以通过Merkle证明来验证块中包含的任何交易。在资源有限的节点(如智能手机)上，轻节点和SPV已被广泛用于扩展区块链系统的计算和存储。

full nodes have to download the entire block tree to validate some transactions in the received blocks.

所谓全节点，会下载区块链中的**所有区块头及交易列表信息**，并根据一些协议规则验证这些交易是否是有效的。

而轻客户端只需下载区块头信息，并假定交易列表中的交易是符合协议规则的。轻客户端根据**共识规则**验证区块，而不是根据协议规则，因此其假定共识规则是诚实的。

轻客户端可以从全节点中接收Merkle证明，其中一笔特定的交易或状态对象被包含在区块头中。[^9]

## Erasure codes

> 纠删码 



## Reed-Solomon算法

> https://zhuanlan.zhihu.com/p/104306038