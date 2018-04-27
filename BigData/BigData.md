<!-- TOC depthFrom:2 depthTo:4 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Introduction to Big data](#introduction-to-big-data)
	- [V's of Big Data](#vs-of-big-data)
- [Foundations for Big Data Systems and Programming](#foundations-for-big-data-systems-and-programming)
	- [HDFS，Hadoop Distributed File System](#hdfshadoop-distributed-file-system)
	- [YARN，The Resource Manager for Hadoop](#yarnthe-resource-manager-for-hadoop)
	- [MapReduce](#mapreduce)
	- [什么时候用Hadoop](#什么时候用hadoop)
	- [XaaS, Anything as a Service](#xaas-anything-as-a-service)
	- [open-source tools built for Hadoop](#open-source-tools-built-for-hadoop)
- [Big Data Modeling and Management](#big-data-modeling-and-management)
	- [Data Mode](#data-mode)
	- [Streaming Data](#streaming-data)
	- [DBMS, Database Management System](#dbms-database-management-system)
		- [Why DBMS](#why-dbms)
		- [Parallel and Distributed DBMS](#parallel-and-distributed-dbms)
		- [DBMS and MapReduce-style System](#dbms-and-mapreduce-style-system)
		- [Shifting Requirements](#shifting-requirements)
		- [Mixed Solution](#mixed-solution)
	- [BDMS, Big Data Management System](#bdms-big-data-management-system)
		- [Desired Characteristic of BDMS](#desired-characteristic-of-bdms)
		- [ACID 和 BASE](#acid-和-base)
		- [CAP Theorem](#cap-theorem)
		- [一些 BDMS介绍](#一些-bdms介绍)
- [Big Data Integration and Processing](#big-data-integration-and-processing)
	- [SQL: Querying Data](#sql-querying-data)
		- [SQL, Structured Query Language](#sql-structured-query-language)
		- [Querying 2 Relations](#querying-2-relations)
		- [Subqueries](#subqueries)
		- [Aggregate 聚合操作](#aggregate-聚合操作)
		- [MongoDB: Documents](#mongodb-documents)
	- [Big Data Integration](#big-data-integration)
		- [一些工具](#一些工具)
	- [Big Data Processing](#big-data-processing)

<!-- /TOC -->


by University of California, San Diego


问题：

1. 什么是 data lake，什么是 data warehouse？
2. 什么是 data stream？


## Introduction to Big data

大数据主要来源：

- Machine，例如一些传感器。 这是最大的数据来源，也是最可靠的。
- People， 例如社交媒体、文字、微博、图片。这是最复杂的数据来源。
- Organization， 公司，例如一些收支记录。这是相对独立的数据来源。

一些基本概念

分布式文件系统（Distributed File System），文件存储在不同位置，提高容错性。

Programming Models，例如 MapReduce，用于分布式计算。


### V's of Big Data

- Volume == Size
- Variety == Complexity，数据复杂度。
- Velocity， real time action， 数据增长速度和处理速度。
- Veracity == Quality， 数据质量，可信度
- Valence == Connectedness，数据关联性（连通性）
- Value ， 数据价值


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


## Big Data Modeling and Management

### Data Mode

什么是 Data Model？ 我的理解就是描述数据结构特征。数据有各种各样的形式。 Data Model 定义了数据的组织结构和处理方式(Structures, Operations, Constraints)。

参考 <https://en.wikipedia.org/wiki/Data_model>

有哪些 Data Model？

- Relational Data Model  (CSV files)
- Semistructured Data Model (JSON)
- Array Data Model (nparray, image)
- Sensor Data
- Vector Space Model (将文档转成 vector，用于比较相似度)
- Graph Data Model

### Streaming Data

流式数据。对一些数据需要实时处理。


### DBMS, Database Management System

#### Why DBMS

Old Times:  Data = Files， 涉及一堆问题：

1. Data redundancy, inconsistency and isolation。 数据冗余、不一致性和隔离
2. Each task a program
3. Data integrity，数据完整性
4. 原子操作。有一些事务是不可分割的，比如转账： A少一点钱，B多一点钱。

这些问题依靠 DBMS 解决。

DBMS 的优点：

1. Declarative query language， 查询语言
2. Data Independence。 解耦。 应用本身不用关心数据的存储方式和位置。
3. 可以优化访问速度
4. Data integrity and security。 数据完整性和安全性。 读写权限，错误恢复。
5. 并发访问


#### Parallel and Distributed DBMS

1. Parallel database system

并行式： 多个备份（有点像Master Slave模式），可以提高并发访问量。具有容错性

2. Distributed database system

分布式数据库系统。数据并不装到一个篮子里。


#### DBMS and MapReduce-style System

二者关注点不同：

1. DBMS： 有效存储、事务、检索
2. MapReduce-style System： Complex Data processing over a cluster of machines.


#### Shifting Requirements

对数据库的需求发生了变化：

1. Data-loading， a new bottleneck

数据加载时间？ 这算什么需求？

2. DBMS 太多的功能用不上。 大部分数据只需要大量的读，但是用不上那么多复杂功能。

3. 同时结合 事务（transaction）和 分析（Analytical）功能

#### Mixed Solution

- DBMS on HDFS
- Spark: Relational operations on in MapReduce system
- Streaming input to DBMS


### BDMS, Big Data Management System

#### Desired Characteristic of BDMS

理想的 BDMS 特性：

- A flexible, Semistructured data model. 灵活的，半结构化的数据模型
- Support today's 'Big Data' types. 支持大数据类型， 例如 文本、图片之类
- A full query language. 查询语言。需要与SQL同样强大！！！
- An efficient parallel query runtime. 高效的并发查询
- Wide range of query size. 支持很大范围的查询大小
- Continuous data ingestion.
- Scale gracefully to manage and query large volume of data. 良好的伸缩性。
- Full data management capability. 数据管理和维护方便。

#### ACID 和 BASE

ACID: 增删改查。但是很难在 BDMS上维护

BASE：

- BA: Basic Availability
- S: Soft State
- E: Eventual Consistency

不太理解意思，大概是指BDMS需要保证基本的可靠性。 每次请求都有响应，但不一定有结果。


#### CAP Theorem

一个分布式系统不可能同时满足：

- Consistency， 一致性
- Availability，可靠性
- Partition Tolerance ？


#### 一些 BDMS介绍

##### Redis

- 键值存储
- 内存
- Partition，分片，大数据
- Replication， Master/Slave
- 很快，高并发

##### Aerospike

> 是一个以分布式为核心基础，可基于行随机存取内存中索引、数据或SSD存储中数据的数据库。它主要用于百G、数T等大数据量并且在数万以上高并发情况下，对性能也有ms读取插入要求的场景。目前主要集中于互联网广告行业，如eXelate、BlueKai、MediaV、 InMobi、 applovin等。

- 键值存储
- Data types: scalar, lists, maps, **geospatial**, large objects
- 高并发？
- 混合架构, 索引存储在 RAM 中，而数据存储在闪存/固态硬盘 (SSD) 上。


##### AsterixDB - a DBMS for Semistructured Data

Apache 开源项目

> Apache AsterixDB 是开源的大数据管理系统 (BDMS)，可以在一个集群中大规模存储，索引，管理和查询语义结构的数据。Hyracks 是 AsterixDB 的底层数据流运行平台。AsterixDB 拥有丰富的数据类型，扩展了 JSON 数据类型，支持立体的和临时的数据。

- 灵活 的数据模型
- 分布式 存储和事务支持
- 快速 数据摄取
- 可伸缩 的并行数据查询执行运行时
- 声明式 查询语言


##### Solr

> Solr是一个高性能，采用Java5开发，基于Lucene的全文搜索服务器。同时对其进行了扩展，提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展并对查询性能进行了优化，并且提供了一个完善的功能管理界面，是一款非常优秀的全文搜索引擎。

全文搜索。。


##### Vertica

> Vertica是一款基于列存储的 MPP （massively parallel processing）架构的数据库。它可以支持存放多至PB（Petabyte）级别的结构化数据。Vertica是由关系数据库大师Michael Stonebraker(2014 年图灵奖获得者)所创建，于2011年被惠普收购并成为其核心大数据平台软件。



## Big Data Integration and Processing

### SQL: Querying Data


#### SQL, Structured Query Language

这里主要理解，如何在 分片机器上（partition）上面做查询。

分两种情况：

1. 直接查询 primary key匹配。 如果 partition 是基于 primary key的，这个就很快，不用在所有的机器上做查询。这样理解：基于 partition的规则信息，减少操作。
2. 查询非 primary key 字段。 这就需要在所有机器上做查询，然后 union结果。 因为partition是并发的，所以速度也很快。

但是这样会产生一个问题： 如果有100个机器， 只有20个有结果，那么还要在另外80个机器上浪费操作么？ 这里可以用到 indexing技术： Given Value， Return records。

以前经常看到 indexing，但是不知道为什么要用。现在大概理解了。

indexing分两种：

- Local Indexing， 每个机器上维护本地的索引。
- Global Indexing， 主机？ 上维护 partition的索引。

索引需要额外的存储空间，但是可以明显提升性能和速度。

#### Querying 2 Relations

理解join操作。 比如查询两个表，进行 join操作。 按照SPJ（Select-Project-Join）步骤，进行。

如果两个表 在不同机器上呢？（partition），会有一些交互行为。

如果使用的是 Spark这样的高级应用， 则不需要关注这些细节。 它会自动处理好这些细节。但是如果仅仅用Hadoop（HDFS）， 需要自己实现这些算法。

#### Subqueries

分两种：

- 不相关的子查询。 这种子查询可以单独提出来，单独执行。与外面查询无关。
- 相关的子查询。 这种子查询基于一些外部条件。 在外部需要什么的时候执行一次子查询。但通常都会缓存优化，避免重复查询。

#### Aggregate 聚合操作

AVG、SUM。

这里关注在 partition上面的 聚合操作。 记住之前 MapReduce的关键三步： Map - Shuffle and Sort - Reduce。 这里在partitions上面的聚合操作也是这样！！！

注意一点： partitions之间会有一些数据交互，但通常都是最小化的。底层的细节不需要关心，只需要了解即可。

#### MongoDB: Documents

`db.collection.find(<query filter>, <projection>).<cursor modifier>`

MongoDB支持的操作：

1. find()， 类似 SQL select
2. comparison:  `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`
3. regular expression:  `$regrex`
4. array operation: `$in`, `$nin`
5. Compound Statements: `$or`, `$and`, `$not`, `$nor`
6. nested elements
7. count, distince
8. aggregation framework: `$match`, `$group`, operation: `$sum`

更具体的操作见官网。

### Big Data Integration

很复杂，大致理解一下：

问题起源于： 不同的数据源，需要集中操作。 例如不同公司的关系型数据库，需要同时操作。 那么问题在于，如何集成数据？

困难在于，如何定义集成数据（中间数据）的 schema，以保证完整性和准确性。

Probabilistic Schema Mapping。

还有一个有趣的概念： 从三个维度去看查询集成数据：

1. 是Materialized 还是 virtual的？ 前者表示中间数据已经准备好。后者表示需要实时操作，从数据源计算。
2. Global schema vs point-to-point？ 不明白
3. One vs multiple data source？

如果三者都是最简单的情况： Materialized、 Global Schema、 One Data Source，则就是典型的中心化数据库； 如果三者都是复杂情况： 则是 P2P Data Integration。

Global Schema: RIM???

Data Exchange：
- Format conversions
- Constraints
- Compressed data，压缩数据。 对于大数据很重要
- Model transformation？？
- Query transformation？？


#### 一些工具

##### Splunk

> Splunk is an American multinational corporation based in San Francisco, California, that produces software for searching, monitoring, and analyzing machine-generated big data, via a Web-style interface.

处理机器产生的大数据。生成界面、图表之类的。

其优势在于什么？？


##### Datameer

发现有很多数据集成分析工具。 那么问题在于，如何需求，如何选择？


### Big Data Processing

pipeline, spark




## Machine Learning With Big Data


一些概念：

1. 监督学习、非监督学习
2. 机器学习算法解决的问题：
	- 分类，classification
	- 回归预测 regression
	- 聚类： cluster
	- Association Analysis 关联性分析


工具：

- KNIME， 主要是界面做的好。但是只能处理小数据。这里就不学了，以后想学再补上。
- Spark，大数据真正利器。


### Data Exploring

主要是为了更好的理解数据，有 Summary Statistics 和 Plot 两种方式。


#### Summary Statistics

- Measures of Location
	- mean
	- median
	- mode（众数，出现次数最多的数）
- Measures of Spread
	- Range
	- Variance
	- Standard deviation
- Measures of Shape
	- skewness，偏态，斜度
	- kurtosis，峰度

#### Plots

- histogram， 分布
- box plot ，盒图
- 相关性分析。


### Data Preparation

主要是做一些特征工程。选择最少的特征，并且保留足够的数据信息。 PCA是很重要的降维手段。

处理 missing value、处理 outlier， 选择特征（添加新的特征）。


### ML: Classification

主要是用一个分类算法，介绍相关的一些概念。 这些概念在别的课程都学了。这里稍微复习一下。


#### DecisionTree Classifier算法相关

- 决策分割方法：  计算度量 impurity： gini？？这个细节上还不是很了解。
- 一些算法参数： 分割深度， 节点上实例最少数量（防止过拟合）
- pre-pruning， 设定一些条件，提前stop，防止决策过深过细。
- post-pruning，一开始构建整个决策树，然后剪去不能改善 泛化误差的分支。 这种计算量很大，但是用的最多。


#### 评估模型相关

- 准确度： accuracy：  预测正确的（TP + TN ) / 所有测试样本数( TP + TN + FP + FN)
- 精确度： precision：  实际P，预测P的（TP) / 所有预测为P的 (TP + FP)
- 召回度： recall：  实际P， 预测P 的（TP） / 测试样本为 P的（TP + FN)

这里我主要想重新理解一下  $F_\beta$，这个值权衡了 精确度和召回度。

 参考 <https://en.wikipedia.org/wiki/F1_score>

$$
\begin{align}
F_\beta &= (1 + \beta^2) \frac{precision * recall}{ \beta^2 * precision + recall} \\
&= 1 / \left(\frac{1}{( 1+ \beta^2) * precsion} + \frac{1}{(1+1/\beta^2) * recall} \right)
\end{align}$$

如何理解呢？ 当 $\beta > 1$ 时， 增大了 precision的权重， 降低了 recall的权重，所以 precision比recall重要。 当 $\beta < 1$ 时， 增大了recall的权重，降低了 precision的权重，所以 recall比 precision重要。


当然直观上，并不能直接看出来。所以一种方便的记忆方法就是 $\beta$ 与 precision 相乘，为了提高 precision的权重。 所以 $\beta> 1$， 则 precision重要。


### ML: Regression

没什么好说的。回归方法。 使用 MSE 评估误差。

线性回归。 没什么好说的。 主要利用各个特征，也可以添加 高次的特征。


### ML: Cluster Analysis

这里只介绍了 K-means 算法。回顾一下算法：

1. 随机分配 k 个中心点
2. 对所有样本计算分配到最近的中心点。（这里需要 定义 distance度量）
3. 对所有中心点构成的簇重新计算中心位置。
4. 重复 2、3 直到 中心点位置不再变化，或者变化较小。

注意 K-means 这个算法通用性比较差。 很容易陷入局部最优解，而且离最优解相差很大。 通常做法是重复几次 K-means算法，评估聚类最优的解： 计算 WSSE(Within-Cluster Sum of Squared Error) 最小的。但 WSSE 仅仅是一个评判标准。

一般 k越大， WSSE越小。 如何选择k？ 感觉、直觉，或者用 Elbow Method ，找一个拐点，来找到 好的 k。

小结：K-means算法的主要问题

1. 何时终止迭代？
2. K-means容易陷入局部最优解，如何摆脱？
3. 如何找到合适的 k？


### ML: Association Analysis

这个没见过。 是一个新的概念，而且用到的算法也没见过。但是感觉解决的问题很老套。

Association Analysis解决的主要问题是：

**在样本中，如果出现了(A, B, C, D) 类似的 item sets， 出现 (X) 的概率是多少？**

Association Analysis的角度比较特殊， 它定义了 item sets，定义了 item sets 到 item sets 的 Rule， 定义了 Rule 的confidence（其实质就是上面问题中的概率）。

还有几个概念：

- n-item set，包含 n 个item 的集合。
- support， frequency of item set，其实就是 item set包含的item同时出现的频率。
- Antecedent，前提条件？ 就是 某个 item set
- Consequent，结果， 也是某个 item set
- Rule(X -> Y) ，  出现X条件下， 同时也出现 Y
- Confidence(X -> Y)， 出现X条件下， 同时也出现 Y的概率

其实这就很简单： $$Confidence = \frac{support(X \cup Y)}{support(X)}$$

通常会设置一个阈值，来定义 有效的 Rule。

Association Analysis Algorithms 有 Apriori、FP Growth、Eclat，都没听过。
- item set creation
- rule generation efficient

对于这样的问题，算法解决的问题是如何最效率的找到所有的 有效的rule。


## Graph Analytics for Big Data

 摘要：

1. Graph的表示形式： 顶点、矩阵（表示边）； 顶点、边集合。


































-
