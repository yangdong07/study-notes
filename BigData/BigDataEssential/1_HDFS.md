

### HDFS: Hadoop Distributed File System

参考资料 ：

- <https://wiki.apache.org/hadoop/>
- <https://www.ibm.com/developerworks/cn/web/wa-introhdfs/index.html>

Hadoop分布式文件系统。

> Apache Hadoop is a framework for running applications on large cluster built of commodity hardware.


一些核心思想：

1. 将处理逻辑放置到数据附近通常比将数据移向应用程序空间更好。 通俗说，就是将运算放到数据附近。



### DataNode 和 NameNode

参考 <http://itm-vm.shidler.hawaii.edu/HDFS/ArchDocOverview.html>

HDFS 包括两种节点形式： NameNode和DataNode。

NameNode管理所有的DataNode，存储文件（blocks）的metadata。 保存在RAM中，速度快。

DataNode是实际存储文件的节点。 其存储形式是 blocks。

层级结构： Cluster -> Rack（机柜）-> PC（主机）-> DataNode -> blocks


#### NameNode  RAM大小 的估算方式

> Estimate minimum Namenode RAM size for HDFS with 1 PB capacity, block size 64 MB, average metadata size for each block is 300 B, replication factor is 3. Provide the formula for calculations and the result.

计算方式：  1PB（1PB = 1024TB = 1024x1024GB) 容量， replication因子为 3，每个block大小为 64MB，则 最多包含：1 PB / (64 MB * 3)  个block的metadata信息。为什么这样计算？因为每个block有3份，但只需要一份metadata。所以总共需要的Namenode的RAM空间为： 1 PB / (64MB x 3) x 300B = 1024 * 1024 * 1024 / 64 / 3 * 300B = 1.6GB

参考 <https://www.cloudera.com/documentation/enterprise/5-10-x/topics/admin_nn_memory_config.html> 的计算方式有点区别。


#### NameNode seek time以及 block读取时间

读取一个block包括两步： 1. 在namenode里seek block的位置； 2. 读取block。

一般要求seek block时间是读取一个block时间的 1%。可以通过这个标准（或要求）来决定block的大小。

例如： SamSung940 PRO SSD:
- reading speed  : 3.5GB/sec
- Namenode RAM seek time:  0.2~0.8ms  , 1% 。
- 估计 block读取时间 ：  20~80ms
- 估计 block大小：  3.5GB/sec * 20~80ms = 70MB ~ 280MB 之间。
- 使用 block大小：128MB
- 实际 128MB 读取时间： 30~40ms。


### HDFS 命令

HDFS 的文件系统操作命令，类似于这种  `hdfs dfs -<uility>`

例如：
- `hdfs dfs -ls`
- `hdfs dfs -mkdir -p`
- `hdfs dfs -chmod`
- `hdfs dfs -find`

大部分与unix文件系统命令类似。不清楚的查一下help即可。这里介绍两个特殊命令：

1. `hdfs dfs -setrep -w 1 xxx.txt`
这个是设置 replication level。
2. `hdfs fsck /user/xxx  -files -blocks -locations`
fsck是HDFS filesystem checking utility ， 用于列出文件的block位置，状态等信息（应该是访问Namenode节点获取）
3. `hdfs fsck -blockId blk_1032323232`
按照 blockId获取block的信息。 注意这里 blockId 不要加上 时间戳信息（列出的blockId的格式是 `blk_<id>_<timestamp>`，注意即可。

关于 fsck的 usage：

```
Usage: hdfs fsck <path> [-list-corruptfileblocks | [-move | -delete | -openforwrite] [-files [-blocks [-locations | -racks]]]] [-includeSnapshots] [-storagepolicies] [-blockId <blk_
Id>]
        <path>  start checking from this path
        -move   move corrupted files to /lost+found
        -delete delete corrupted files
        -files  print out files being checked
        -openforwrite   print out files opened for write
        -includeSnapshots       include snapshot data if the given path indicates a snapshottable directory or there are snapshottable directories under it
        -list-corruptfileblocks print out list of missing blocks and files they belong to
        -blocks print out block report
        -locations      print out locations for every block
        -racks  print out network topology for data-node locations
        -storagepolicies        print out storage policy summary for the blocks
        -blockId        print out which file this blockId belongs to, locations (nodes, racks) of this block, and other diagnostics info (under replicated, corrupted or not, etc)
```
