
### Hadoop MapReduce

一句话表示MapReduce： Mapper -> Shuffle & Sort -> Reducer。 分布式处理、混洗排序、归约计算结果。最重要的是，即使某个部分出现错误，可以重新计算而不影响全局。 容错性。

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

在 Shuffle & Sort过程中，主要产生 intermediate files： 在本地磁盘， 如果这个过程中有缺失，可以从Map中重新计算获取。

Reduce过程中，如果节点出现错误，集中到另一个节点计算。

在 YARN 中， 通过 Resource Manager管理节点资源。 通过 Application Master管理MapReduce任务（Task）

MapReduce Framework 的要求就是这些，会将这些工作在底层实现好。


### MapReduce Streaming

注意一个概念： 把计算放到数据附近。

在MapReduce的过程中，需要的仅仅是 Mapper 和 Reducer。这两个都可以通过脚本（bash或者python）实现。要求也很简单：

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

1. mapper = `wc -l` , no reducer

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

4. python脚本
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


再次提及这个概念： 将计算放到数据附近。也就是说 在mapreduce计算任务中，将文件（files）、压缩包（archives）、jar库（libjars）复制到各个节点（Node Manager）， 由节点的容器（实际执行任务的单位）去访问这些资源，并执行。


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

传递一个 `word_pattern` 变量，可以在脚本中通过 `os.environ['word_pattern']` 获取。


#### task progress

不仅可以向任务传递一些配置，也可以在任务执行中汇报任务进度。

```python
import sys
print("task progress, xxx", file=sys.stderr)
```

### testing 测试

测试又是一系列问题。

1. Unit Testing，单元测试， 利用一些工具（例如 pytest）对脚本功能进行测试，单元测试
2. Integration Testing，集成测试 ？？
3. System Testing， 单机测试
4. Acceptance Testing， 可以理解成灰度测试，先对一小数据集进行测试，及早发现问题。如果没有问题，推广到全部。


### Combiner

这个问题我见过。

在map的时候，输出 (key, value) 值。 比如单词计数这个任务。 在一个节点上运行的map任务，可能产生很多个重复的 （a，1）这样的。 为了减少后面的工作量，可以先用Combiner组合一下。

在wordcount这个例子中， Combiner 等于 reducer的处理，即计数统计。所以有下面的命令：

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

前面三段描述了问题。 最后一段说明 Speculative 方案： 当一个节点的任务落后于其他节点时，在其他节点上（没有任务的节点上）去执行相同的任务，取一个最快的结果，将其他任务 discard掉。 这是一种用空间换时间的做法。


### Compression


|Compression Format| Splittable| Comments| Hadoop CompressionCodec
|---|---|---|----|
|.deflate  .gz(gzip)| NO| Uses DEFLATE algorithm | org.apache.hadoop.io.compress.DefaultCodec GzipCodec
|.bz2(bzip)| YES | more effective than gzip, but slower compression | Bzip2Codec
|.lzo| YES | decompres way faster than gzip, compres less effiecient | com.hadoop.compression.lzo.LzopCodec
|.snappy| NO| faster than LZO for decompression | SnappyCodec


options
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

### hadoop streming 练习作业

#### wordcount

mapper.py
```python
%%writefile mapper.py

import sys
import re

reload(sys)
sys.setdefaultencoding('utf-8') # required to convert to unicode

for line in sys.stdin:
    try:
        article_id, text = unicode(line.strip()).split('\t', 1)
    except ValueError as e:
        continue
    words = re.split("\W*\s+\W*", text, flags=re.UNICODE)
    for word in words:
        print >> sys.stderr, "reporter:counter:Wiki stats,Total words,%d" % 1
        print "%s\t%d" % (word.lower(), 1)
```

reducer.py
```python
%%writefile reducer.py

import sys

current_key = None
word_sum = 0

for line in sys.stdin:
    try:
        key, count = line.strip().split('\t', 1)
        count = int(count)
    except ValueError as e:
        continue
    if current_key != key:
        if current_key:
            print "%s\t%d" % (current_key, word_sum)
        word_sum = 0
        current_key = key
    word_sum += count

if current_key:
    print "%s\t%d" % (current_key, word_sum)
```

```bash
%%bash

OUT_DIR="wordcount_result_"$(date +"%s%6N")
NUM_REDUCERS=8

hdfs dfs -rm -r -skipTrash ${OUT_DIR} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.jab.name="Streaming wordCount" \
    -D mapreduce.job.reduces=${NUM_REDUCERS} \
    -files mapper.py,reducer.py \
    -mapper "python mapper.py" \
    -combiner "python reducer.py" \
    -reducer "python reducer.py" \
    -input /data/wiki/en_articles_part \
    -output ${OUT_DIR} > /dev/null

hdfs dfs -cat ${OUT_DIR}/part-00000 | head
```

#### 按出现次数最多的顺序排序

 这里需要两个任务，这个花了我很长时间。 这个练习太坑人了。题目没有说清楚。完全靠猜。

注意几点：

1. 第二个任务不需要脚本，可以直接用 `partition.keycomparator` 排序实现。参考 <http://52.36.65.137/iSR_Grav/entries/entry-2#tabs1-jvp>，以及 sort命令使用方法。
2. 第二个任务只用一个reducer输出。第二个任务的输入是第一个任务的输出。
3. 如果输出是倒序的，那么直接用 `sed`命令就可以取第7行数据。

```bash
%%bash

OUT_DIR="wordcount_result_"$(date +"%s%6N")
NUM_REDUCERS=8
OUT_DIR2="popular_result"

hdfs dfs -rm -r -skipTrash ${OUT_DIR} > /dev/null
hdfs dfs -rm -r -skipTrash ${OUT_DIR2} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.jab.name="Streaming wordCount" \
    -D mapreduce.job.reduces=${NUM_REDUCERS} \
    -files mapper.py,reducer.py \
    -mapper "python mapper.py" \
    -combiner "python reducer.py" \
    -reducer "python reducer.py" \
    -input /data/wiki/en_articles_part \
    -output ${OUT_DIR} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.output.key.comparator.class=org.apache.hadoop.mapred.lib.KeyFieldBasedComparator \
    -D stream.num.map.output.key.fields=2 \
    -D stream.map.output.field.separator="\t" \
    -D mapreduce.partition.keycomparator.options="-k2,2nr" \
    -D mapred.jab.name="Streaming wordCount2" \
    -D mapreduce.job.reduces=1 \
    -files mapper.py,reducer.py \
    -mapper /bin/cat \
    -reducer /bin/cat \
    -input ${OUT_DIR} \
    -output ${OUT_DIR2} > /dev/null

hdfs dfs -cat ${OUT_DIR2}/part-00000 | sed -n "7p"
```

#### 统计 stop words数量，并计算占比

思路是在 mapper里加一条统计信息，当出现stopwords时，就计1。 然后在输出的logs里，找到Total words和 Stop words两个统计信息，计算输出就可以了。

```python
%%writefile mapper.py

...
stop_words = set()
with open("stop_words_en.txt") as f:
    for line in f:
        stop_words.add(line.strip().lower())

for line in sys.stdin:
    ...
    for word in words:
        print >> sys.stderr, "reporter:counter:Wiki stats,Total words,%d" % 1
        if word in stop_words:
            print >> sys.stderr, "reporter:counter:Wiki stats,Stop words,%d" % 1
        print "%s\t%d" % (word.lower(), 1)
```

 解析文件
```python
%%writefile counter_process.py

import sys

stop_words = 0
total_words = 0

with open(sys.argv[1]) as f:
    for line in f:
        line = line.strip().lower()
        if line.startswith('stop words'):
            stop_words = int(line.split('=')[1])
        elif line.startswith('total words'):
            total_words = int(line.split('=')[1])

print('%.2f' % (stop_words / float(total_words) * 100))
```

```bash
%%bash

OUT_DIR="wordcount_result"
NUM_REDUCERS=8
LOGS="stderr_logs.txt"

hdfs dfs -rm -r -skipTrash ${OUT_DIR} > /dev/null
hdfs dfs -get /datasets/stop_words_en.txt .

yarn jar ...
    -output ${OUT_DIR} > /dev/null 2> $LOGS

python ./counter_process.py $LOGS
cat $LOGS >&2
```
 注意以上步骤，先是将 stderr重定向到 `$LOGS`，然后用python脚本处理 `$LOGS`，最后再将 `$LOGS`放出来。主要是为了评分系统工作。


#### 找出名字，然后按数量倒序输出

这一题题意描述特别差。 什么是名字？ 符合以下条件：

1. 不以数字开头
2. 以大写字母开头，其他字母都是小写字母
3. 如果这个单词符合条件（2），同时也有不符合条件（2）的，则看其统计比例，如果不符合条件（2）的占比小于 0.5%，则认为是名字。

思路是：

1. 对于符合条件（1， 2）的（按正则表达式匹配），map值为 : (word, 1, 1)，第一个值表示 isname，第二个值是计数。
2. 对于其他情况，map值为: (word, 0, 1)
3. 第一个reducer负责计数。 不仅统计 count，还要统计 isname的个数
4. 第二个mapreduce job， map负责过滤， 对于 isname / count > 0.995 的，输出；其他都过滤掉。
5. 第二个reducer负责倒序排序，以第二个字段数值倒序排序。

```python
%%writefile mapper.py
#/usr/bin/python

import sys
import re

reload(sys)
sys.setdefaultencoding('utf-8') # required to convert to unicode

name_pattern = re.compile("^[A-Z][0-9a-z_]*$")

for line in sys.stdin:
    try:
        article_id, text = unicode(line.strip()).split('\t', 1)
    except ValueError as e:
        continue
    words = re.split("\W*\s+\W*", text, flags=re.UNICODE)
    for word in words:
        print >> sys.stderr, "reporter:counter:Wiki stats,Total words,%d" % 1
        if name_pattern.match(word):
            print "%s\t%d\t%d" % (word.lower(), 1, 1)
        else:
            print "%s\t%d\t%d" % (word.lower(), 0, 1)
```

```python
%%writefile reducer.py

import sys

current_key = None
word_sum = 0
isname = 0

for line in sys.stdin:
    try:
        key, match, count = line.strip().split('\t', 2)
        count = int(count)
        match = int(match)
    except ValueError as e:
#         print e
        continue
    if current_key != key:
        if current_key:
            print "%s\t%d\t%d" % (current_key, isname, word_sum)
        isname = 0
        word_sum = 0
        current_key = key
    isname += match
    word_sum += count

if current_key:
    print "%s\t%d\t%d" % (current_key, isname, word_sum)
```

```python
%%writefile mapper2.py

import sys

for line in sys.stdin:
    try:
        word, isname, count = line.strip().split('\t', 2)
        isname = int(isname)
        count = int(count)
    except ValueError:
        continue

    if isname / float(count) > 0.995:
        print "%s\t%d" % (word, count)
```

```bash
%%bash

INPUT="/data/wiki/en_articles_part"
OUTPUT1="wordcount_result"
OUTPUT2="names"

hdfs dfs -rm -r -skipTrash ${OUTPUT1} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.jab.name="Streaming wordCount" \
    -D mapreduce.job.reduces=8 \
    -files mapper.py,reducer.py \
    -mapper "python mapper.py" \
    -combiner "python reducer.py" \
    -reducer "python reducer.py" \
    -input ${INPUT} \
    -output ${OUTPUT1} > /dev/null

hdfs dfs -rm -r -skipTrash ${OUTPUT2} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.output.key.comparator.class=org.apache.hadoop.mapred.lib.KeyFieldBasedComparator \
    -D stream.num.map.output.key.fields=2 \
    -D stream.map.output.field.separator="\t" \
    -D mapreduce.partition.keycomparator.options="-k2,2nr" \
    -D mapred.jab.name="Streaming nameCount" \
    -D mapreduce.job.reduces=1 \
    -files mapper2.py \
    -mapper "python mapper2.py" \
    -reducer "/bin/cat" \
    -input ${OUTPUT1} \
    -output ${OUTPUT2} > /dev/null

! hdfs dfs -cat ${OUTPUT2}/part-00000 | sed -n '5p'

```

#### 按组统计数字

其实可以只用一个mapreducer，但是题目非要用两个mapreducer。

1. 第一个mapreducer负责单词计数，这个没什么
2. 第二个mapreducer，map负责将 (word, count) 映射为 (key, word, count)，key是 word按字母顺序排列后的字符串。
3. 第二个mapreducer，reducer负责将key值相同的，统计总数和 unique word计数。然后拼接输出。

 这里只给出第二个mapreducer的代码

```python
%%writefile mapper2.py

import sys
import re

for line in sys.stdin:
    try:
        word, count = line.strip().split('\t', 1)
        count = int(count)
    except ValueError as e:
        continue

    key = ''.join(sorted(word))
    print "%s\t%s\t%d" % (key, word, count)
```

```python
%%writefile reducer2.py

import sys

cur_key = None
word_count = 0
words = set()

for line in sys.stdin:
    try:
        key, word, count = line.strip().split('\t', 2)
        count = int(count)
    except ValueError as e:
        continue
    if cur_key != key:
        if cur_key:
            word_list = list(words)
            word_list.sort()            
            print "%d\t%d\t%s" % (word_count, len(words), ','.join(word_list))
        word_count = 0
        words = set()        
        cur_key = key
    word_count += count
    words.add(word)

if cur_key:
    print "%d\t%d\t%s" % (word_count, len(words), ','.join(word_list))

```

```bash
%%bash

INPUT="/data/wiki/en_articles_part"
OUTPUT1="wordcount"
OUTPUT2="groupcount"

hdfs dfs -rm -r -skipTrash ${OUTPUT1} > /dev/null

hdfs dfs -get /datasets/stop_words_en.txt .

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.jab.name="Streaming wordCount" \
    -D mapreduce.job.reduces=8 \
    -files mapper1.py,reducer1.py,stop_words_en.txt \
    -mapper "python mapper1.py" \
    -reducer "python reducer1.py" \
    -input ${INPUT} \
    -output ${OUTPUT1} > /dev/null

hdfs dfs -rm -r -skipTrash ${OUTPUT2} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D mapred.jab.name="Streaming wordCount" \
    -D mapreduce.job.reduces=1 \
    -files mapper2.py,reducer2.py \
    -mapper "python mapper2.py" \
    -reducer "python reducer2.py" \
    -input ${OUTPUT1} \
    -output ${OUTPUT2} > /dev/null

hdfs dfs -cat ${OUTPUT2}/part-00000 | grep 'english,helsing,hesling,shengli,shingle'
```


#### TFIDF

计算所有word的 TFIDF值。

1. IDF，假设一个word 在 Dt 个文档中出现，则  $idf = 1 / \log(1 + Dt)$。出现次数越多，则值越小。表示越普通
2. TF， 假设一个文档中，一个word出现 Nt 次，文档单词总数 为 N（不包括 stop words）， 则 $tf = Nt / N$。
3. TF-IDF， 这两个值的乘积即为 TF-IDF

TF-IDF 有两个key（或者说参数、键）： word 和 artcile_id。 IDF 是全局的； TF 是文档内的。

一开始思路有误区，走了很多弯路： 1个mapreduce job计算 TF， 1个计算 IDF， 然后再JOIN起来计算 TF-IDF， JOIN的操作，参考 <https://blog.matthewrathbone.com/2016/02/09/python-tutorial.html> 写的很好。

一开始我总是认为 TF和 IDF不可能在一个Mapreduce任务里面搞定。其实可以。

在map任务里面，因为每一行是一个article， 那么 article单词的总数是知道的。那么我需要输出 `word, article, 1, count` 就可以了。 count表示 article单词的总数。(也可以输出 `word, article, 1/count`，还可以简化计算）， 这样在reduce 任务里面，我就可以计算 tf了。

同时，在shuffle & sort里， 按照 `word, article` 排序，可以累积每个word 每个 article计数， 这样可以计算每个word 对于多少个 唯一的（unique） article， 这就得到了 Dt，也就可以算出 IDF值。

对于每个word，缓存一下 每个article的 tf值，然后在结算的时候，计算 idf值， 然后输出每个article的 tf * idf 即可。

按道理说， 全局信息不能出现在 mapper中，但由于输入一行就是一个article，所以article单词总数可以放到 mapper中。

##### Mapper
```python
%%writefile mapper.py

import sys
import re

reload(sys)
sys.setdefaultencoding('utf-8')

stop_words = set()
with open('stop_words_en.txt') as f:
    for line in f:
        stop_words.add(line.strip().lower())

for line in sys.stdin:
    try:
        article_id, text = unicode(line.strip()).split('\t', 1)
    except ValueError as e:
        continue
    text = re.sub("^\W+|\W+$", "", text, flags=re.UNICODE)
    words = re.split("\W*\s+\W*", text, flags=re.UNICODE)
    words = filter(lambda w: w not in stop_words, map(lambda w: w.lower(), words))
    count = len(words)
    for word in words:
        print "%s\t%s\t%.10f" % (word, article_id, 1.0 / count)
```

##### Reducer
```python
%%writefile reducer.py

import sys
import math

current_word = None
word_frequency = dict()

for line in sys.stdin:
    try:
        word, article_id, tf_unit = unicode(line.strip()).split('\t', 2)
        tf_unit = float(tf_unit)
    except ValueError as e:
        continue        

    if word != current_word:
        # article
        if current_word:
            idf = 1.0 / math.log(1 + len(word_frequency))
            for article_id, tf in word_frequency.iteritems():
                print "%s\t%s\t%f" % (current_word, article_id, tf * idf)

        current_word = word
        word_frequency = dict()

    if article_id not in word_frequency:
        word_frequency[article_id] = tf_unit
    else:
        word_frequency[article_id] += tf_unit        

if current_word:
    # article
    if current_word:
        idf = 1.0 / math.log(1 + len(word_frequency))
        for article_id, tf in word_frequency.iteritems():
            print "%s\t%s\t%f" % (current_word, article_id, tf * idf)
```

##### Hadoop Streaming

```bash
%%bash

OUT_DIR="tfidf"
NUM_REDUCERS=3

hdfs dfs -rm -r -skipTrash ${OUT_DIR} > /dev/null

yarn jar /opt/cloudera/parcels/CDH/lib/hadoop-mapreduce/hadoop-streaming.jar \
    -D stream.num.map.output.key.fields=2 \
    -D stream.map.output.field.separator="\t" \
    -D map.output.field.separator="\t" \
    -D mapreduce.partition.keypartitioner.options=-k1,1 \
    -D mapred.output.key.comparator.class=org.apache.hadoop.mapred.lib.KeyFieldBasedComparator \
    -D mapreduce.partition.keycomparator.options="-k1,2" \
    -D mapred.jab.name="Streaming wordCount" \
    -D mapreduce.job.reduces=${NUM_REDUCERS} \
    -files mapper.py,reducer.py,stop_words_en.txt \
    -mapper "python mapper.py" \
    -reducer "python reducer.py" \
    -partitioner org.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner \
    -input /data/wiki/en_articles_part \
    -output ${OUT_DIR} > /dev/null

hdfs dfs -cat ${OUT_DIR}/part* | awk '$1 == "labor" && $2 == "12" {print;}'
```

这个配置也是做了很久才摸索出来的，可以多个reducer工作的。关键是 partition的方式。

注意：

1. `stream.num.map.output.key.fields=2` 这个说明了 key的字段有两个
2. `stream.map.output.field.separator="\t"` 说明了 字段是按照 '\t' 分割的
3. `map.output.field.separator="\t"` 这个不清楚作用是什么，加上和去掉都没有什么关系。似乎与上一个作用重复了
4. `mapreduce.partition.keypartitioner.options=-k1,1` 这个非常关键，决定了 partition的工作方式。 其实没有很复杂，简单粗暴，直接按照word 分区： 相同的word会分到同一个区，这就是我想要的。
5. `-partitioner org.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner` 带上这个
6. `mapreduce.partition.keycomparator.options="-k1,2"` 这个决定了 partition内部 sort的方式。

在算这个的过程中，踩了不少坑。

1. 如果配置了 `stream.num.map.output.key.fields=2` 这个，不配置 `mapreduce.partition.keypartitioner.options=-k1,1`。 则partition默认是按照 `k1,2` 即两个字段的key值来进行分区。这就导致一个word出现在不同的分区（因为 article_id 不同，分区键是 word + article_id） ， 从而导致 在reduce 计算过程中， idf的计算错误：每个分区中一个word对应的article的数量可能不是完整的。
2. 上面这个坑，试验了很多很多次，才找出来。 此外在实验过程中，了解了 streming的测试方法，以及 awk命令。

至此， 计算TF-IDF，输入每行是一个article的任务， 可以用这样一个 mapreduce任务完美完成。


### Map Reduce 应用

#### Map-Side Join

 如果数据集比较小，可以用 Distribute Cache的方式，将数据集共享到各个节点。（文件位置可以是 hdfs系统里的文件）

这种方式成为 Map-Side Join


#### Reduce-Side Join

如果有很多个大的数据集， 则使用Reduce-Side Join。 前面主要处理一些map工作，甚至可以将不同数据混在一起（加一个标签）

#### Secondary Sort

注意为了避免 partition的 skew，比如某个key值有50%的数据，集中在一台机器上是不好的。可以采用2个字段partition，平均分配。


#### Tabular Data : FieldSelectMapReduce

就是一种 处理 类似 tsv格式方式。


#### 拆分mapreduce工作

注意 `_SUCCESS` 文件，可以作为上一个mapreduce成功完成的标志。

#### DataSkew， Salting

如果遇到key值不均衡的情况（比如有很多null），可以通过 “加盐”的方式，使之均衡

一种加盐方法：  `null ->  'null_' + random.randint(0, 100)`
