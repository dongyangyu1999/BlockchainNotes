> 基于Hyperledger Fabric的区块链农产品溯源方案

# 介绍

本文设计了一种改进的联盟链农产品溯源方案。



## 内容

本文的系统将农业基地、物流、销售、监管机构等参与方规划进入 Hyperledger Fabric 联盟区块链的相应Channel，对相应信息进行查询和管理。

> 每一个Channel都是一个虚拟的区块链网络，Channel和Channel之间相互隔离；Peer是参与区块链网络的服务节点。对于每一个Channel里的所有Peer，都会为该Channel维护相同的账本数据。

本文的方案建立了四个Channel。

* Channel1: 农业基地、监管机构；
* Channel2: 物流、监管机构；
* Channel3: 零售、监管机构；
* Channel4: 农业基地、物流、零售、监管机构。

本文通过一个苹果溯源系统的例子展示了该原型系统的基本运作模式，业务逻辑清晰易懂，难点主要在区块链的集群部署以及fabric的使用，还有智能合约的编写。

# 补充资料

## 概念解释

**Peer**

Fabric 网络中的节点，表现为一个运行着的docker容器。可以与网络中的其他peer进行通信，每个peer都在本地保留一份ledger的副本

**Org**

一个或多个peer组成org。在文件`crypto-config.yaml`中可以设置如下block指定一个org中peer数量。比如，count为2，则该org存在`peer0` 和 `peer1`两个节点。

**Channel**

channel指一个在两个或多个<u>特定网络成员间的专门以机密交易为目的而建立的私有"子网"</u>。channel中包含一个或多个org。每个channel拥有一个账本，并共享给channel内的所有peer。同一个channel内的org可以部署到多台机器上。Fabric为channel间数据通信和同步提供了多套解决方案，比如基于zookeeper得kafka等。



## RESTful API

> REST全称是Representational State Transfer，中文意思是表征性状态转移。 它首次出现在2000年Roy Fielding的博士论文中，Roy Fielding是HTTP规范的主要编写者之一。 他在论文中提到："我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、**适宜通信**的架构。REST指的是一组架构约束条件和原则。" 如果一个架构符合REST的约束条件和原则，我们就称它为RESTful架构。
>
> REST本身并没有创造新的技术、组件或服务，而隐藏在RESTful背后的理念就是使用Web的现有特征和能力， 更好地使用现有Web标准中的一些准则和约束。

要理解RESTful架构，最好的方法就是去理解Representational State Transfer这个词组的意思，每一个词代表了什么涵义。

1. **资源（Resources）**

REST的名称"表现层状态转化"中，省略了主语。"表现层"其实指的是"资源"（Resources）的"表现层"。

**所谓"资源"，就是网络上的一个实体，或者说是网络上的一个具体信息。**它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。你可以用一个URI（统一资源标识符Uniform Resource Identifier）指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。

所谓"上网"，就是与互联网上一系列的"资源"互动，调用它的URI。URI既可以看成是资源的<u>地址</u>，也可以看成是资源的<u>名称</u>。如果某些信息没有使用URI来表示，那它就不能算是一个资源， 只能算是资源的一些信息而已。URI的设计应该遵循<u>可寻址性原则，具有自描述性</u>，需要在形式上给人以直觉上的关联。这里以github网站为例，给出一些还算不错的URI：

- https://github.com/git
- https://github.com/git/git
- https://github.com/git/git/blob/master/block-sha1/sha1.h

2. **表现层（Representation）**

"资源"是一种信息实体，它可以有多种外在表现形式。**我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。**

比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。

<u>URI只代表资源的实体，不代表它的形式</u>。严格地说，有些网址最后的".html"后缀名是不必要的，因为这个后缀名表示格式，属于"表现层"范畴，而URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。

3. **状态转化（State Transfer）**

访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。

互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，**如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”。而这种转化是建立在表现层之上的，所以就是“表现层状态转化”。**

客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的动词，GET、POST、PUT、DELETE。它们分别对应四种基本操作：

* GET用来获取资源
* POST用来新建资源（也可以用于更新资源）
* PUT用来更新资源
* DELETE用来删除资源。

**小结**

综合上面的解释，总结一下什么是RESTful架构：

　　（1）每一个URI代表一种资源；

　　（2）客户端和服务器之间，传递这种资源的某种表现层；

　　（3）客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。



# 参考

http://www.ruanyifeng.com/blog/2014/05/restful_api.html

https://www.runoob.com/w3cnote/restful-architecture.html

http://www.ruanyifeng.com/blog/2011/09/restful.html