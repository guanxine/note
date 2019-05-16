## SLA(Service-Level Agreement): 服务等级协议
服务提供者对客户的一种服务承诺
1. 可用性: 4个9, 99.99% 或者每年 50 分钟的系统中短: (24 × 60 × 60 × 0.0001) = 8.64s(99.99%)
2. 准确性: 是否允许某些数据不准确或者丢失，如果允许这样的情况发生，用户可以接受的概率是多少
3. 系统容量: 系统能够支持的预期负载量，一般会以每秒的请求数为单位表示(qps, rps)
4. 延迟: 系统收到用户的请求到响应这个请求之间的时间间隔: p99/p95=1s, 100 个请求有1/5个请求超过了1s

## 可扩展性
1. 水平扩展： 现有的系统中增加新的机器节点
2. 垂直扩展： 不改变系统中机器数量的情况下，升级现有机器的性能，比如增加机器内存

关系数据库，表之间有join操作，所有数据放在单机系统中，很难支持水平扩展．而 NoSQL 型的数据库天生支持水平扩展，所以这类存储系统的应用越来越广，如 BigTable,MongoDB 和 Redis

## 一致性
水平扩展可以提高可用性，但是如何保证系统中不同的机器节点在同一时间，接收到和输出的数据是一致的?

机器之间需要同步．然而发送信息也有失败的可能，比如信息丢失或者有节点正好宕机而无法接收．
* 强一致性：更新后，读到的数据是更新后的数据．更新一个节点，其余节点需要同步，同步完成才能读取．牺牲延迟(高)
* 弱一致性：更新后，读到的数据可能是更新后的数据，也可能是更新前的数据．但经过一段时间后，所有节点都是更新后的．
* 最终一致性：弱一致性特殊形式．没有更新的条件下，最终所有访问都是最后更新的值．

实际中，强一致性很难实现，应用最广的是最终一致性: 12306 买票和转账都是最终一致性

## 持久性
数据一旦被成功存储就可以一直继续使用．即使系统中的节点下线，宕机或损坏
---

## 无边界数据(Unbounded Data)和有边界数据(Bounded Data)
无边界数据又称流数据(Streaming Data), 无限的数据集
有边界数据是一种有限的数据集：保存好的数据中，数据库中的数据，csv中的数据．

无边界的数据按照时间窗口提取一小份出来
移动支付中的交易数据看作是无边界数据，但是按照20190505提取出来的数据呢，就变成了有边界的数据了

有边界数据可以看作无边界数据的子集

## 事件时间(Event time)和处理时间(Precessing Time)

事件时间：一个数据实际产生的时间点
处理时间：处理数据的系统架构实际接收到这个数据的时间点

## 批处理
输入数据是有边界数据，输出也是有边界数据．
关系更多的是事件时间：支付宝年度账单

日志分析，计费程序，数据仓库：根据收集好的数据事件时间，将信息合并为静态快照，并将他们聚合为每周，每月，每季度的报告

## 流处理
足够快，低延时，能够处理来自各种数据源的大规模数据．
当流处理在一定时间间隔 (毫秒) 内产生逻辑正确的结果时，这种架构可以定义为实时处理(Real-time Processing)
速度快的原因是，在数据到达磁盘之前对其进行了分析

应用：实时监控(传感器，新闻源，点击网页)，实时商业智能．


## 工作流系统中的设计模式
1. 复制模式(Copier Pattern)
单个数据集复制到多个数据处理模块中：同一份数据需要采用多种不同的数据处理转换

2. 过滤模式(Filter Pattern)

3. 分离模式(Splitter Pattern)

4. 合并模式(Joiner Pattern)

在设计大规模数据处理系统的时候，先设计一个工作流系统图


## 发布/订阅模式(Publish/Subscribe Pattern)
流处理框架常用
### 消息(Message)
分布式中，各个组件(Component)需要沟通: 消息, 格式: JSON/XML, 自定义
### 消息队列(Message Queue)
在发布和订阅模式中起的是一个持久化缓冲(Durable Buffer)的作用

消息发送方可以发送任意消息到这个消息队列中，消息队列在接收到消息后将消息存好，直到消息的接收方确认已经从这个队列拿到了消息，才会将这个消息从队列中删除
场景
1. 发送方需要向大量接收方广播消息
2. 发送方在向接收方发送消息后，无需接收方进行实时响应
3. 最终一致性

## CAP 三选二
###C:一致性(Consistency)
分布式下所有操作都像是单机上完成的一样，状态一致
###A:可用性(Availability)
分布式系统中，任意非故障的服务器都必须对客户端的请求产生相应
当系统满足可用性时，不管什么状况(除非所有服务器都崩溃)，都能返回消息
###P:分区容错性(Partition-tolerance)
系统允许网络丢失从一个节点发送到另一个节点的任意多条消息

分布式系统中，出现一些故障，使得部分节点无法连通，整个网络被分为几块区域，数据分散在这些无法连通的区域中的情况，就被叫做 "分区"

即使出现分区这样的"错误", 系统也要能"容忍"．
就算分区出现了，系统也必须能够返回消息.

现代网络通信中，节点故障和网络丢包这样情况时常发生，如果没有分区容错，意味着节点间通讯出现任何错误的话，我们所用的系统就不能继续工作了．

大部分情况下，系统都会保留P属性，而在C和A中选一个．论文中论证了最多可以保留两个属性，CP/AP/CA

1. CP: BigTable,Hbase,MongoDB,Redis 存储框架放弃了高可用(High Availablity) 而选择了 CP 属性．
2. AP: 
3. CA: Kafka
放弃P属性的Kafka Replication
通过　zookeeper 选举一个 Leader 节点，先写　Leader 节点，然后同步其他节点．其他节点挂了，不影响;主节点挂了，zookeeper 再选举

自己的系统保留了什么属性，用的系统保留了什么属性

## Lambda 架构

## Spark 
1. 数据尽可能的在内存中
2. 分区存储，天然并行
3. 存储了每一步的计算结果之间的依赖关系，大大提升了数据容错性和错误恢复的正确率

弹性分布式数据集(Resilient Distributed Dataset -> RDD)

传统 MR 慢的原因是有向无环图的中间计算结果需要写入硬盘这样的稳定存储介质来防止运行结果丢失。
每次调用中间计算结果都需要一次硬盘的读取，读取操作，潜在的数据赋值和序列化操作大大提高了延迟。

在分布式内存中存储中间计算的结果， RDD 是一个基于分布式内存的数据抽象
### RDD: 分区，不可变， 并行操作
1. 分区：RDD 中每个元素是一个大数组，数组中的每个元素代表一个分区(Partition), 物理存储上每个分区指向一个存放在内存或者硬盘中的数据块(Block), 而这些数据块是独立的，他们可以被存放在系统中的不同节点。各节点的数据会尽可能的存放在内存中，只有当内存没有空间时才会存入硬盘。最大化减少硬盘读写的开销
2. 不可变性：每一个RDD都是可读的，它所包含的分区信息不可改变。可以对现有的RDD进行转换操作，得到新的RDD作为中间计算的结果。
对于中间结果的RDD，需要记录他是通过哪个RDD进行哪些转换操作得来的。即依赖关系。而不用立刻存储计算出的数据本身。在一个N步计算模型中，第N步出错了，可以从N-1步出发，无需重复整个N步计算。 
3. 并行操作：由于单个 RDD 的分区特性，使得它天然支持并行操作。即不同节点上的数据可以被分别处理，然后产生新的 RDD.

### RDD 结构 
比较重要的
1. Partitions: 代表了RDD中数据的逻辑结构，每个Partition会映射到某个节点内存或硬盘的一个数据块。决定了 RDD 的分区方式，Hash， Range(根据key的排序进行分区)，此外我们可以创建自定义的 Partitioner.
2. 依赖关系: Dependencies 父 RDD 和 子 RDD 之间的关系
    + 窄依赖(Narrow Dependency): 父分区可以一一对应到子分区： map, filter,union 等操作，每个分区可以并行操作。
    + 宽依赖(Wide Dependency)： 父RDD的每个分区被多个子RDD的分区使用: join,groupBy，必须等父RDD的所有分区被计算好之后才能开始处理。