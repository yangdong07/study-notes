

# Big Data Essentials


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


## HDFS 命令

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

## Data Modeling and File Formats

每个大数据课程都会提到的两个概念。

1. Data Modeling 是处理数据时，包括在程序中使用中用的概念： 以何种方式组织数据，有效的操作数据。
2. File Formats 是存储数据时，用到的概念： 如何有效的存储数据、传输数据，确保数据的完整性和正确性？

### Relational data model

关系性数据模型。 Table、Row、Column。

### Graph data model

点（Vertices，entities）、边（Edges，relations）。

### Unstructured Data

Technically, all data is structured at least as a byte sequence. Usually, means "not structured enough for a task"

就是对一个任务来说，结构化不够的意思。

### File Format(Storage Format)

Primary Function: to transform between raw bytes and programmatical data structures(serialization & deserialization)

最关键的功能是在存储格式（raw bytes） 与 程序数据格式之间转换。

一般有几个指标评估一个 File Format

|File Format| Space Efficiency | (Encoding /Decoding) Speed | Data Types | Splittable| Extensibility
|--|--|--|--|--|--|
| CSV & TSV| BAD 因为都是字符串，数字也用字符串| GOOD| Only Strings| Splittable W/O Header| BAD
| JSON | BAD(worse than CSV) | GOOD ENOUGH| Strings, numbers, booleans, maps, lists| Splittable if 1 document per line | YES


|Binary Format| Space Efficiency | (Encoding /Decoding) Speed | Data Types | Splittable| Extensibility
|--|--|--|--|--|--|
| SequenceFile| Moderate to GOOD | GOOD | ANY W/SER./DESER. CODE| Splittable | NO
| Avro| Moderate to GOOD| GOOD With CodeGen | JSON-LIKE | Splittable | YES
| RCFile|GOOD| Moderate to GOOD, Less I/O | Byte Strings | Splittable | NO
| Parquet|

注： 
1. Sequence File是  First binary format implemented in Hadoop。 存储 键值对序列。每个record包含一个键值信息。或者每个block包含若干records。records或者blocks序列存储。具体见 <https://wiki.apache.org/hadoop/SequenceFile>


### Compression

- Block-level
- File-level


|Codecs | Compression Speed| Decompression| Ratio|
|--|--|--|--|
|Bzip2   | 12~14MiB/s  |38~42MiB/s   | 4.02~4.80  |
|Gzip| 16~90MiB/s| 250~320MiB/s| 2.77~3.43|
|LZO| 77~150MiB/s | 290~314MiB/s| 2.10~2.48
|Snappy| 200MiB/s| 475MiB/s| 2.05|

[MiB = mebibyte](https://en.wikipedia.org/wiki/Mebibyte)

什么时候用 Compression？

- CPU-bound： 不要用压缩。用压缩不仅不会提高效率，反而会增加CPU计算负担
- I/O-bound： 可以从压缩中收益。


## Hadoop MapReduce

MapReduce 框架是很重要的程序模型。

参考

- <https://www.zhihu.com/question/23345991>



### Unreliable Components

Node Failure:

- Fail-Stop，某个节点挂掉了
- Fail-Recovery：某个节点挂掉了，但是会自动恢复
- Byzantine： 某个节点的行为不可预知：任何情况都有可能，信息可能被篡改，等等。

涉及到 拜占庭将军问题： 分布式对等网络通信容错问题。


### MapReduce 概念 及容错性（Fault Tolerance）

Map -> Shuffle & Sort -> Reduce

在Map过程中，每个节点如果出现错误，可以重新计算，不影响其他节点。

在 Shuffle & Sort过程中，主要产生 intermediate files： 在本地磁盘， 如果这个过程中有缺失，可以从Map中重新计算获取。

Reduce过程中，如果节点出现错误，集中到另一个节点计算。

在 YARN 中， 通过 Resource Manager管理节点资源。 通过 Application Master管理MapReduce任务（Task）

MapReduce Framework 的要求就是这些，会将这些工作在底层实现好。


### MapReduce Streaming

注意一个概念： 把计算放到数据附近。

在MapReduce的过程中，需要的仅仅是 Mapper 和 Reducer。这两个都可以通过脚本（bash或者python）实现。要求也很简单：

- Mapper
  - define input format
  - process data
  - define output format
- Reducer
  - define input format
  - aggregate sorted data by key
  - process data
  - define output format

注意 脚本与工作流的接口是  `stdin` 和 `stdout`。 如果要汇报任务进度，则通过 `stderr`


#### 一般用法示例

1. mapper = `wc -l` , no reducer

```bash
HADOOP_STREAMING_JAR="/path/to/hadoop-streaming.jar"

yarn jar $HADOOP_STREAMING_JAR \
         -mapper 'wc -l'   \
         -numRedcueTasks 0 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```

2. mapper = `wc -l`,  reducer = `awk '{line_count += \$1} END {print line_count}'`

```bash
HADOOP_STREAMING_JAR="/path/to/hadoop-streaming.jar"

yarn jar $HADOOP_STREAMING_JAR \
         -mapper 'wc -l'   \
         -reducer "awk '{line_count += \$1} END {print line_count}'"
         -numRedcueTasks 1 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```

3. mapper = `wc -l`, reducer = 'reducer.sh'
```bash
HADOOP_STREAMING_JAR="/path/to/hadoop-streaming.jar"

yarn jar $HADOOP_STREAMING_JAR \
         -mapper 'wc -l'   \
         -reducer './reducer.sh' \
         -file reducer.sh  \
         -numRedcueTasks 1 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```

4. python脚本
```bash
yarn jar $HADOOP_STREAMING_JAR \
         -mapper 'python mapper.py'   \
         -reducer 'python reducer.py' \
         -files reducer.py,mapper.py  \
         -numRedcueTasks 1 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```

#### Distributed Cache 配置

`-files`, `-archives`, `-libjars`


再次提及这个概念： 将计算放到数据附近。也就是说 在mapreduce计算任务中，将文件（files）、压缩包（archives）、jar库（libjars）复制到各个节点（Node Manager）， 由节点的容器（实际执行任务的单位）去访问这些资源，并执行。


#### 环境变量

注意 mapper、reducer的执行位置在节点上。其环境变量包括：

- mapreduce_task_id
- mapreduce_task_partition

也可以传递环境变量参数，例如：

```bash
yarn jar $HADOOP_STREAMING_JAR  -D word_pattern="\w+\d+" \
         -mapper 'python mapper.py'   \
         -reducer 'python reducer.py' \
         -files reducer.py,mapper.py  \
         -numRedcueTasks 1 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```

传递一个 `word_pattern` 变量，可以在脚本中通过 `os.environ['word_pattern']` 获取。


#### task progress

不仅可以向任务传递一些配置，也可以在任务执行中汇报任务进度。

```python
import sys
print("task progress, xxx", file=sys.stderr)
```

### testing 测试

测试又是一系列问题。

1. Unit Testing，单元测试， 利用一些工具（例如 pytest）对脚本功能进行测试，单元测试
2. Integration Testing，集成测试 ？？
3. System Testing， 单机测试
4. Acceptance Testing， 可以理解成灰度测试，先对一小数据集进行测试，及早发现问题。如果没有问题，推广到全部。


### Combiner

这个问题我见过。

在map的时候，输出 (key, value) 值。 比如单词计数这个任务。 在一个节点上运行的map任务，可能产生很多个重复的 （a，1）这样的。 为了减少后面的工作量，可以先用Combiner组合一下。

在wordcount这个例子中， Combiner 等于 reducer的处理，即计数统计。所以有下面的命令：

```bash
yarn jar $HADOOP_STREAMING_JAR \
         -mapper 'python mapper.py'   \
         -combiner 'python reducer.py'
         -reducer 'python reducer.py' \
         -files reducer.py,mapper.py  \
         -numRedcueTasks 1 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```

有时候combiner需要单独写处理脚本。


### Partitioner

 参考 <https://blog.csdn.net/zhanglh046/article/details/78557468>

简单一句话：分区就是更好的将map任务的结果均匀分配给reducer。

有时候map的输出结果的key值是比较复杂的，有时候需要多个字段进行比较。

```bash
yarn jar $HADOOP_STREAMING_JAR \
         -D stream.num.map.output.key.fields=2
         -mapper 'python mapper.py'   \
         -reducer 'python reducer.py' \
         -files reducer.py,mapper.py  \
         -numRedcueTasks 5 \
         -input  /data/wiki/en_ariticls \
         -output wc_mr
```
 上面这个就是用map输出的头2个字段作为key。


### Comparator

这个概念就简单了。就是定义 shuffle & sort中的 key比较器。

### Speculative Task

问题是：有些节点任务执行的慢，有些节点任务执行的快。 总的运行时间总是被慢的任务节点拖慢。如何解决这个问题？

参考

- <http://dongxicheng.org/mapreduce/hadoop-speculative-task/>
- <https://stackoverflow.com/questions/15164886/hadoop-speculative-task-execution>

> One problem with the Hadoop system is that by dividing the tasks across many nodes, it is possible for a few slow nodes to rate-limit the rest of the program.

> Tasks may be slow for various reasons, including hardware degradation, or software mis-configuration, but the causes may be hard to detect since the tasks still complete successfully, albeit after a longer time than expected. Hadoop doesn’t try to diagnose and fix slow-running tasks; instead, it tries to detect when a task is running slower than expected and launches another, equivalent, task as a backup. This is termed speculative execution of tasks.

> For example if one node has a slow disk controller, then it may be reading its input at only 10% the speed of all the other nodes. So when 99 map tasks are already complete, the system is still waiting for the final map task to check in, which takes much longer than all the other nodes.

> By forcing tasks to run in isolation from one another, individual tasks do not know where their inputs come from. Tasks trust the Hadoop platform to just deliver the appropriate input. Therefore, the same input can be processed multiple times in parallel, to exploit differences in machine capabilities. As most of the tasks in a job are coming to a close, the Hadoop platform will schedule redundant copies of the remaining tasks across several nodes which do not have other work to perform. This process is known as speculative execution. When tasks complete, they announce this fact to the JobTracker. Whichever copy of a task finishes first becomes the definitive copy. If other copies were executing speculatively, Hadoop tells the TaskTrackers to abandon the tasks and discard their outputs. The Reducers then receive their inputs from whichever Mapper completed successfully, first.

前面三段描述了问题。 最后一段说明 Speculative 方案： 当一个节点的任务落后于其他节点时，在其他节点上（没有任务的节点上）去执行相同的任务，取一个最快的结果，将其他任务 discard掉。 这是一种用空间换时间的做法。


### Compression


|Compression Format| Splittable| Comments| Hadoop CompressionCodec
|---|---|---|----|
|.deflate  .gz(gzip)| NO| Uses DEFLATE algorithm | org.apache.hadoop.io.compress.DefaultCodec GzipCodec
|.bz2(bzip)| YES | more effective than gzip, but slower compression | Bzip2Codec
|.lzo| YES | decompres way faster than gzip, compres less effiecient | com.hadoop.compression.lzo.LzopCodec
|.snappy| NO| faster than LZO for decompression | SnappyCodec


options
```
-D mapreduce.compress.map.output=true            # map输出压缩
-D mapreduce.map.output.compression.codec=...    # Codecs
```
```
-D mapreduce.output.compress=true          # reducer最终结果压缩
-D mpareduce.output.compression.codec=...  # Codecs
```

对于一些 cold data（不常用的data），可以用 bzip2方式， 空间效率高，压缩时间长。
对于经常访问的 hot data， 可以用 lzop方式，空间效率低，但是解压时间快。
