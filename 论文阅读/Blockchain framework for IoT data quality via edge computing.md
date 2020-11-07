> 《Blockchain framework for IoT data quality via edge computing》论文笔记
>
> **通过边缘计算实现物联网数据质量的区块链框架**，呈现了一个基于区块链的架构，介绍边缘计算层和一个用于提升数据质量和错误数据监测的算法。

# 文章结构

* 介绍
  * 该体系结构在边缘计算范式下具有计算分布。这使得优化<u>物联网和区块链</u>之间的连接具有可能性。
  * 这个混合系统的另一部分是数据管理，拥有一个大数据生态系统，可以方便地管理区块链中的大量数据。
* 基于物联网的区块链架构
* 实验结果
* 结论



# 文章内容

本文主要目的是提高数据质量以及检测错误数据，因此设计了一种基于博弈论的算法，该算法基于物联网节点收集的数据，在新体系结构的边缘计算层上运行。

* 前提：
  * 数据都存储在安全的区块链上
  * 忽略传感器精度上或者探测错误数据中可能发生的错误
* 案例演示：
  * 使用Smart Home智能家居，通过<u>温度检测</u>来解释该结构。

## 温度矩阵

将收集到的数据按照传感器现实中放置的位置，放入矩阵中。（为了实现矩阵的排布，通过网格的方式给这些传感器进行排序）

![image-20201105091329971](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105091329971.png)

## 符号注释

|符号|注释|
| ------ | :----------------------------------------- |
| n (>2) | the number of players in the game(1, …, n) |
| N      | the set of players |
| S | A **coalition**: a subset of N |
| ![image-20201105092210641](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105092210641.png) | the set of all coalitions |
| u(x) | N中的合作博弈是一个**函数u**(characteristic feature of the game)，该函数为每个联盟$S_i$⊆$S$分配一个实数*u(Si)*。<br />In addition one has the condition u(∅) = 0. |

In our case, the set of players is the set of ordered sensors S and the
characteristic function u is defined as:  
![image-20201105092651446](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105092651446.png)

such that, for each coalition of sensors, u = 1 or 0 if that particular coalition can vote or not respectively (see eq.(2, 3))
![image-20201105092730072](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105092730072.png)

## Cooperative sensor coalitions

> 协同传感器联盟，先了解Cooperative games([合作博弈](#合作博弈))，

通过与周围传感器进行温度比较，而达成联盟。“周围”怎么定义呢？论文中是通过矩阵中[曼哈顿距离](#曼哈顿距离)小于一为标准的。
![image-20201105093738079](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105093738079.png)

## A characteristic function to find cooperative temperatures.

> 本节讲述的是如何确定传感器的最终数据（温度），也就是如果有的节点是错误的，该联盟如何确定协同温度。

首先，计算每个联盟中所有节点（传感器）的平均温度。
![image-20201105095055128](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105095055128.png)

$T_{s_{i}}^{k}$表示在第$k$次迭代中，$S_i$附近一带所有传感器的平均温度，V是该联盟中的节点（传感器）数量

第二步是计算<u>每个传感器温度与平均温度的差值</u>的绝对值
![image-20201105095553953](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105095553953.png)

第三步是使用(6)中的插值绝对值计算出**置信区间**（展现这个参数的真实值有一定概率落在测量结果的周围的程度，其给出的是被测量参数的测量值的可信程度），误差为1%
![image-20201105095959950](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105095959950.png)

第四步是统计推断（hypothesis test），如果传感器的温度在区间$I_{s_{i}}^{k}$内，则属于投票联盟，否则不属于投票联盟:
![image-20201105100234144](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105100234144.png)

第五步是不断修复的过程：特征函数将迭代地重复这个过程(k为迭代的次数)，直到迭代中的所有传感器**都**属于投票联盟。
在每一次迭代k中，可以得到第k步中联盟$S_j$ 的**收益向量*payoff vector***$\left(P V\left(S_{j}^{k}\right)\right)$, (1≤j≤n，其中n为联盟中传感器的数量)
![image-20201105100708299](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105100708299.png)

博弈迭代的终止条件是$PV\left(S_{j}^{k}\right)=PV\left(S_{j}^{k+1}\right)$，也就是说令$P V\left(S_{j}^{k}\right)=\left(u^{k}\left(s_{1}\right), \ldots, u^{k}\left(s_{n}\right)\right)$并且令$P V\left(S_{j}^{k+1}\right)=\left(u^{k+1}\left(s_{1}\right), \ldots, u^{k+1}\left(s_{n}\right)\right)$。当两个收益向量包含相同元素时，迭代工程就中止。过程如下：![image-20201105100629726](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105100629726.png)

由于所提出的博弈是一个合作博弈，其解的概念是一个参与者联盟，我们称之为**博弈均衡** (game equilibrium (GE) )。该博弈中的GE被定义为**投票超过一半的最小联盟**。获胜的联盟必须满足以下条件:

1. 联盟PV(收益向量)元素的总和必须高于所投选票的half+1
   ![image-20201105101703417](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201105101703417.png)
2. 联盟是最大化的（在其联盟的收益向量$PV (S_j^{k})$中，联盟元素个数最大，不为0）

因此，所提博弈的解是在博弈的每一步k处形成的所有可能的联盟中，验证了两个条件的联盟




# 相关资料

## WSN

> 无线传感器网络Wireless sensors networks(WSN) ，它是物联网的关键技术。

WSN网络是由部署在监测区域内大量的廉价微型传感器节点组成，通过无线通信方式形成的一个自组织网络。

无线传感器网络能够实时监测和采集网络分布区域内的各种监测对象的信息（如温度，声音，湿度等），以实现复杂的制定范围内目标监测和跟踪

## 曼哈顿距离

> 曼哈顿距离（Manhattan Distance）是种使用在几何度量空间的几何学用语，用以标明两个点在标准坐标系上的<u>绝对轴距总和</u>。

![image-20201104151917581](Blockchain%20framework%20for%20IoT%20data%20quality%20via%20edge%20computing.assets/image-20201104151917581.png)

* 红线是和蓝色、黄色等价的曼哈顿距离。
* 绿色代表欧氏距离，也就是直线距离

曼哈顿距离——两点在南北方向上的距离加上在东西方向上的距离，即d(i,j)=|xi-xj|+|yi-yj|。对于一个具有正南正北、正东正西方向规则布局的城镇街道，从一点到达另一点的距离正是在南北方向上旅行的距离加上在东西方向上旅行的距离，因此，曼哈顿距离又称为<u>出租车距离</u>。

曼哈顿距离不是距离不变量，当坐标轴变动时，点间的距离就会不同。曼哈顿距离示意图在早期的计算机图形学中，屏幕是由像素构成，是整数，点的坐标也一般是整数，原因是<u>浮点运算很昂贵，很慢而且有误差</u>，如果直接使用AB的欧氏距离(欧几里德距离：在二维和三维空间中的欧氏距离的就是两点之间的距离），则必须要进行浮点运算，如果使用AC和CB，则只要计算加减法即可，这就大大提高了运算速度，而且不管累计运算多少次，都不会有误差。

## Knowledge Discovery in Database (KDD)

> 知识发现是从各种信息中，根据不同的需求获得知识的过程。知识发现的目的是向使用者屏蔽原始数据的繁琐细节，从原始数据中<u>提炼出有效的、新颖的、潜在有用的知识</u>，直接向使用者报告。



## 合作博弈

> **Cooperative games(合作博弈)** are defined by the fact that players can cooperate with each other in order to achieve <u>a mutual benefit</u>. Once the
> players have agreed to cooperate among themselves, a coalition
> should be formed.  



# ETL（数据仓库技术）

> ETL，是英文Extract-Transform-Load的缩写，用来描述将数据从来源端经过抽取（extract）、转换（transform）、加载（load）至目的端的过程。ETL一词较常用在数据仓库，但其对象并不限于数据仓库。



# 结论

本文利用博弈论提出了一种分布式自组织协同算法。该算法已应用于物联网设备采集的数据。此外，还提出了一种基于区块链的架构来提高数据安全性。这种体系结构的新颖之处在于，它提出了一个边缘计算层，在该层执行算法以<u>提高数据质量</u>和<u>错误数据检测</u>



# 启发：

* 论文中的解释非常严谨，一些理所当然的条件也标明在内，如第四页中规定“Coalitions cannot be formed by a single sensor”联盟不能仅有单个传感器组成。
* 通过**[统计推论](https://blog.csdn.net/huangkaihong/article/details/106741357) (Statistical Inference)**取得一个范围内的平均数据，通过迭代再进行比较的方式去修复可能存在问题的结点数据。