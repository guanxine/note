## Hadoop入门

1. GFS: gfs-sosp2003.pdf
2. MapReduce: mapreduce-osdi04.pdf
3. BigTable: bigtable-osdi06.pdf
4. Zookeeper: chubby-osdi06.pdf
5. Impala: CIDR15_Paper28.pdf
6. 其他论文: http://mobitec.ie.cuhk.edu.hk/iems5709Spring2016/
7. 入门材料: CDH http://www.slideshare.net/iwrigley/louisville-intro-tohadoop
8. Hadoop内部培训: MR和Spark探讨2.pdf
9. mapreduce工作原理: http://horicky.blogspot.com/2008/11/hadoop-mapreduce-implementation.html
10. mapreduce性能优化: http://www.slideshare.net/cloudera/mr-perf

问题
1. GFS和MapReduce是全新架构吗, 开发动机是什么?
2. 如何配置一个典型hadoop集群的硬件, 关键因素是什么?
3. Word Count编程模型中, map函数的输入是什么?
4. map任务个数和reduce任务个数对性能有什么影响,应该如何调优?
5. HDFS+MapReduce集群中有哪些守护进程,分别负责什么?
6. 如何用Ganglia监控hadoop集群, 关注指标是哪些?
7. Spark的设计动机是什么,有了MapReduce为什么还需要Spark?
8. Spark中RDD的好处是什么?
9. Zookeeper在hadoop生态系统中是什么角色?
10. SQL-on-Hadoop有几种典型架构, 好处是什么?

## 实时处理入门

1. Storm: ss-storm.pdf
2. Kafka: Kafka.pdf
3. Storm: storm101training-public-v2-140915032000-phpapp01.pptx 
4. Kafka: kafka101training-public-v2-140818033637-phpapp01.pptx
5. http://storm.apache.org/tutorial.html
6. http://storm.apache.org/documentation/Concepts.html
7. http://www.michael-noll.com/blog/2012/10/16/understanding-the-parallelism-of-a-storm-topology/
8. http://www.michael-noll.com/blog/2013/06/21/understanding-storm-internal-message-buffers/

问题
1. Kafka和其他消息队列的区别是什么,设计重点是什么?
2. Kafka中数据持久化是如何设计的?
3. Kafka中通常Consumer是瓶颈还是producer是瓶颈?瓶颈是什么?为什么?
4. 为什么要用storm来处理kafka的读写?
5. 函数式编程和storm的关系是什么?
6. storm的并发度是怎么确定的?
7. storm几种grouping方法对于性能有什么影响?
8. storm的容错机制是什么? nimbus或者supervisor失效会有什么后果?
9. 如何确定storm的worker个数?其对性能有什么影响?
10. 10台pc服务器 (16 cores, 64GB RAM, 12 disks, 1Ｇb网络带宽) 用Kafka+Storm的架构, 处理2KB 500字段的消息, 吞吐量大概多大,如何估计?