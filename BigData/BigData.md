

# Big Data

by University of California, San Diego


## Introduction to Big data

大数据主要来源：

- Machine，例如一些传感器。 这是最大的数据来源，也是最可靠的。
- People， 例如社交媒体、文字、微博、图片。这是最复杂的数据来源。
- Organization， 公司，例如一些收支记录。这是相对独立的数据来源。


### V's of Big Data

- Volume == Size
- Variety == Complexity，数据复杂度。
- Velocity， real time action， 数据增长速度和处理速度。
- Veracity == Quality， 数据质量，可信度
- Valence == Connectedness，数据关联性（连通性）
- Value ， 数据价值


## Foundations for Big Data Systems and Programming

一些基本概念

分布式文件系统（Distributed File System），文件存储在不同位置，提高容错性。

Programming Models，例如 MapReduce，用于分布式计算。


Hadoop Ecosystem

### HDFS，Hadoop Distributed File System

### YARN，The Resource Manager for Hadoop

- Resource Manager
- Node Manager
- Application Master，协商者，向Resource Manager请求资源，要求Node Manager完成工作。
- Container

### MapReduce

MapReduce 的优点： 简化并行计算，充分利用分布式系统。

MapReduce的HelloWorld程序： WordCount，包括三个步骤：

1. Map： 每个Node上WordCount
2. Shuffle and Sort： 将相同key放到一个Node上。
3. Reduce：计数。

一般就包含着三个步骤。

MapReduce 不适用于以下情景：

1. 经常改变的数据
2. 依赖性（相关性）强的任务
3. 交互性不友好，每次计算需要读入所有数据，运行完之后才能得到结果。



### 什么时候用Hadoop

Hadoop并不是全能的，只适用于一部分应用场景，例如 大数据、并行计算任务。

Hadoop不适用于：

- 小数据集
- Advanced Algorithms
- Infrastructure Replacement
- Task level parallelism
- Random Data Access


### XaaS, Anything as a Service

IaaS: Infrastructure as a Service, 例如 AWS EC2
PaaS: Platform as a Service， 例如  Google App Engine
SaaS：Software as a Service， 例如 Dropbox。

### open-source tools built for Hadoop

- Hive， for SQL-like queries.
- Storm， Stream Data 流数据处理。
- Spark， In-memory 数据处理
- Giraph， 图片数据处理


Gephi可以看图形数据，比如社交网络。
