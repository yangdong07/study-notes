

# Big Data Essentials: HDFS, MapReduce and Spark RDD


## HDFS: Hadoop Distributed File System

参考资料 ：

- <https://wiki.apache.org/hadoop/>
- <https://www.ibm.com/developerworks/cn/web/wa-introhdfs/index.html>

Hadoop分布式文件系统。

> Apache Hadoop is a framework for running applications on large cluster built of commodity hardware.


一些核心思想：

1. 将处理逻辑放置到数据附近通常比将数据移向应用程序空间更好。 通俗说，就是将运算放到数据附近。



### DataNode 和 NameNode

参考 <http://itm-vm.shidler.hawaii.edu/HDFS/ArchDocOverview.html>

HDFS 包括两种节点形式： NameNode和DataNode。

NameNode管理所有的DataNode，存储文件（blocks）的metadata。 保存在RAM中，速度快。

DataNode是实际存储文件的节点。 其存储形式是 blocks。

层级结构： Cluster -> Rack（机柜）-> PC（主机）-> DataNode -> blocks
