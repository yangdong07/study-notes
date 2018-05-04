


## Spark

关于Spark的RDD概念，参考 <http://sharkdtu.com/posts/spark-rdd.html> 写的很清晰。


>RDD全称叫做弹性分布式数据集(Resilient Distributed Datasets)，它是一种分布式的内存抽象，表示一个只读的记录分区的集合，它只能通过其他RDD转换而创建，为此，RDD支持丰富的转换操作(如map, join, filter, groupBy等)，通过这种转换操作，新的RDD则包含了如何从其他RDDs衍生所必需的信息，所以说RDDs之间是有依赖关系的。基于RDDs之间的依赖，RDDs会形成一个有向无环图DAG，该DAG描述了整个流式计算的流程，实际执行的时候，RDD是通过血缘关系(Lineage)一气呵成的，即使出现数据分区丢失，也可以通过血缘关系重建分区，总结起来，基于RDD的流式计算任务可描述为：从稳定的物理存储(如分布式文件系统)中加载记录，记录被传入由一组确定性操作构成的DAG，然后写回稳定存储。另外RDD还可以将数据集缓存到内存中，使得在多个操作之间可以重用数据集，基于这个特点可以很方便地构建迭代型应用(图计算、机器学习等)或者交互式数据分析应用。


### Spark 基础概念


- RDD： Resilient Distributed Datasets， Resilient表示弹性，灵活性和容错性。 Distributed表示分布式。
- RDD is a readonly, partitioned collection of records
- partitions： RDD提供接口访问其所有的 partition。
- read only： RDD是只读的，只能通过transformation创建一个新的RDD。在创建过程中就产生了依赖关系。
- RDD tracks dependency： RDD提供接口访问其所有 dependency。
- DAG，有向无环图。由于 RDD的 transformation 总是创建新的RDD，所以全部依赖关系构成有向无环图。
- transformation： 没有side effect的计算。 side effect的意思是不对外界产生影响。
- iterator： RDD的 transformation 并没有实际产生计算，也没有结果输出。它提供了一个iterator，可以访问partition的records， 这个iterator是建立在依赖关系parent的 iterator基础之上的。
- actions: actions trigger computation and processing of the datasets
- actions: actions 不一定在本地机器上运行
- driver program： spark有一个 driver program和 SparkContext，用于执行程序，分布任务，监控任务在分布机器上的运行，收集结果。一些计算并不一定发生在local上，也可能是分布式的。所以写程序时要注意一些环境依赖。
- narrow dependency 和 wide dependency。
- join： 默认是inner join，两边都有的key，联结在一起。 outer join包括了只有一边有的key，另一边填充 null值。
- Fault-tolerance in MapReduce： 两个关键点，一个是输入和输出都是可靠的；另一个是map和reducer的执行都是确定性的（deterministic）和 side effect free。
- Fault-tolerance in Spark：同样的两个关键点。 
- Side effect： Side effect是指运行会对外界产生影响，比如写数据库。 Fault Tolerance要求 transformation计算没有side effect，无论哪个partition出现错误，都可以重启，反复计算而不影响外界。
- idempotency: Actions是有side effect的，但是要求 idempotency （幂等），即无论多少次重复相同输入，输出结果不变。
- deterministic： 确定性，无论多少次调用，都返回确定性的值。


### Spark 进阶概念

#### SparkContext

- SparkContext，在Driver Program中， SparkContext（sc）负责与Cluster Manager（比如 YARN、mesos、SparkStandalone ClusterManager) 请求资源（节点资源、执行器），然后分配执行任务，监控执行器状态（executor）。
- 执行过程： Action -> Job -> Job Stages -> Tasks。 第一步， 调用一个action；第二步： sc spawn一个job。第三步：通过 job scheduler 将任务分解成若干 stages； 第四步： 从job stage创建 tasks，由执行器执行。
- Job stage： is a pipelined computation spanning between materialization boundaries。 not immediately executable
- Task: a job stage bound to particular partitions. immediately executable
- materialization: happens when reading, shuffling or passing data to an action. 注意： narrow dependencies allow pipelining。 我的理解就是 narrow dependencies 可以做成 Pipeline，可以分解成一个 Job Stage。 而 wide dependencies 之间由于是一个shuffle过程，最好能 materialization
- SparkContext的其他作用： 监听 executors的心跳（Fault-tolerance），调度并发jobs（同一个Application内），不同Application的动态资源分配。


#### Caching & Persistence

如果一个 RDD在计算时重复使用，可以缓存（caching）以提高效率。

`rdd.persist(storageLevel)`

第一次计算之后缓存结果。

storageLevel：

- DISK_ONLY
- MEMORY_ONLY
- MEMORY_AND_DISK： 优先保存到内存。如果内存溢出，则存到 DISK
- DISK_ONLY_2, MEMORY_ONLY_2, MEMORY_AND_DISK_2： 一个备份。（2 replica）

`rdd.cache()` = `rdd.persist(MEMORY_ONLY)`


**Best Practices**

1. 对于 interactive sessions： cache preprocessed data
2. 对于 batch computations：
    1. cache dictionaries
    2. cache other datasets that are accessed multiple times


#### Broadcast variables

动机（motivation）：（只读）资源共享。

例如分析日志文件（1TB），有一个IP字典（1GB）。如果有1000个Tasks，需要 1000 * 1GB = 1TB的流量。

可以使用 Broadcast Variable， 类似于 torrent（种子？）的共享方式，只需要 1~2GB的流量。

```python
sc = SparkContext(conf=...)

my_dict_rdd = sc.textFile(...).map(...).filter(...)
my_dict_data = my_dict_rdd.collect()

# Broadcast Variable
broadcast_var = sc.broadcast(my_dict_data)

my_data_rdd = sc.textFile(...).filter(lambda x: x in broadcast_var.value)
```

#### Accumulator variable

动机： 方便一些统计计数。

Accumulator Variable 是一个 read-write variable ， 在tasks之间共享。

注意： 只能进行 increment操作！！

- tasks： write（increment）
- driver program： read allowed only by driver program

### 一些统计概念

- 样本估计（Estimation），其实就是参数估计。比如对一个binary分布，只有一个参数 $p$，即出现1的概率。要估计出这个参数，可以用 $\hat{p} = \frac{1}{n} \sum_i^n{X_i}$。
- 如何评估这个 estimation？  可以理解： 如果样本越多，此估计就会越精确。
- Standard Deviation  $S_{\hat{p}_n}$
- Confidence Interval ，置信区间。 定义为： $[C_L, C_U]: P(C_L \le p \le C_U) \ge 1 - \alpha$
- 95% 置信区间：  $\alpha = 0.05$
- 一句话理解置信区间： 真实参数落在此区间的概率。这是频率学派的解释： 参数是固定的，样本是随机的。（贝叶斯学派： 观察到的样本是固定的，参数是随机的）
- Bootstrap Confidence Interval


### Spark 应用实战。。


#### aggregateByKey

尽量避免用 groupByKey。

或者 reduceByKey也少用。为什么？ 还是因为 data skew。如果一个key有很多值，那么按照key partition的话，很多值都落到一个节点上进行运算。

`aggregateByKey(zeroValue, seqFunc, combFunc, numPartitions=None, partitionFunc=<function portable_hash>)[source]`

>Aggregate the values of each key, using given combine functions and a neutral “zero value”. This function can return a different result type, U, than the type of the values in this RDD, V. Thus, we need one operation for merging a V into a U and one operation for merging two U’s, The former operation is used for merging values within a partition, and the latter is used for merging values between partitions. To avoid memory allocation, both of these functions are allowed to modify and return their first argument instead of creating a new U.`

参考 <https://devopsrecipe.wordpress.com/2016/10/17/pyspark-aggregatebykey/>

zeroValue提供了一个 累积的开始值。

seqFunc 是作用在partition内部的，其形式为 ：  `lambda acc, value: acc + value`，类似于这种。 其中 acc为累加值，value为 (key, value) 中的value

combFunc 是作用于所有partitions， 其形式为： `lambda acc1, acc2: acc1 + acc2`，类似这种。



#### 计算 npmi

这是 honor题。题目意思理解了，但是很难做对。总是说有错误。说是第一行或者最后一行有问题。

TODO: 后面待改进。

npmi 是一种词语搭配匹配的度量。

第一步，计算所有单词的 probability
```python
import re

def parse_article(line):
    try:
        article_id, text = unicode(line.rstrip()).split('\t', 1)
        text = re.sub("^\W+|\W+$", "", text, flags=re.UNICODE)
        words = re.split("\W*\s+\W*", text, flags=re.UNICODE)
        return words
    except ValueError as e:
        return []

wiki = sc.textFile("/data/wiki/en_articles_part/articles-part", 16).map(parse_article)

stop_words = set()

with open('/datasets/stop_words_en.txt') as f:
    for line in f:
        stop_words.add(line.strip().lower())

all_words = wiki.flatMap(lambda words: [w.lower() for w in words])
non_stop_words = all_words.filter(lambda w: w not in stop_words)
total_count = non_stop_words.count()
word_count = non_stop_words.map(lambda w: (w, 1)).reduceByKey(lambda x, y: x + y)
word_probability = dict(word_count.map(lambda w: (w[0], w[1] / float(total_count))).collect())
```

第二步： 计算 bigram的probability，筛出来 大于500个的。
```python

def parse_bigrams(words):
    bigrams = []
    for i in range(len(words) - 1):
        key = '#'.join([words[i].lower(), words[i+1].lower()])
        bigrams.append(key)
    return bigrams

bigrams = wiki.flatMap(parse_bigrams)

total_bigrams = bigrams.count()

bigram_count = bigrams.map(lambda x: (x, 1)).reduceByKey(lambda x, y: x + y).filter(lambda x: x[1] >= 500)

bigram_probability = dict(bigram_count.map(lambda x: (x[0], x[1] / float(total_bigrams))).collect())
```

第三步，计算 npmi

```python
import math
npmi_list = []

for key in bigram_probability:
    a, b = key.split('#')
    if a in stop_words or b in stop_words:
        continue
    pmi = math.log(bigram_probability[key] / word_probability.get(a, 1) / word_probability.get(b, 1))
    npmi = pmi / -math.log(bigram_probability[key])
    npmi_list.append((npmi, '_'.join([a, b])))

npmi_list.sort(reverse=True)

print len(npmi_list)
for _, word in npmi_list[:39]:
    print word
```


#### BFS 计算最短路径

spark 还能写图算法，也是麻烦。


```python
from pyspark import SparkConf, SparkContext
sc = SparkContext(conf=SparkConf().setAppName("MyApp").setMaster("local"))

def parse_edge(s):
    user, follower = s.split("\t")
    return (int(user), int(follower))

def step(item):
    prev_v = item[0]
    prev_d, parent = item[1][0]
    next_v = item[1][1]
    return (next_v, (prev_d + 1, prev_v))

def complete(item):
    v, old_d, new_d = item[0], item[1][0], item[1][1]    
    return (v, new_d if old_d is None else old_d)


n = 2  # number of partitions
# edges = sc.parallelize([(i, i+1) for i in range(10)]).cache()
edges = sc.textFile("/data/twitter/twitter_sample_small.txt").map(parse_edge).cache()
forward_edges = edges.map(lambda e: (e[1], e[0])).partitionBy(n).persist()


source = 12
target = 34
d = 0

distances = sc.parallelize([(source, (d, None))]).partitionBy(n)

target_found = None
while True:
    #  (v, (d, parent_v)) -> (v, ((d, parent_v), next_v)) -> (next_v, (d + 1, v))
    candidates = distances.join(forward_edges, n).map(step)

    # (next_v, (d+1, v)) -> (v, ((d, parent_v), (d, parent_v))) / (v, (old_d, new_d))
    # ->  (v, new_d if old_d is None else old_d)
    new_distances = distances.fullOuterJoin(candidates, n).map(complete, True).persist()

    # break if meet target
    t = new_distances.filter(lambda i: i[0] == target)
    if t.count() > 0:
        target_found = t.take(1)[0]
        break

    # break if no new distance
    count = new_distances.filter(lambda i: i[1][0] == d + 1).count()
    if count > 0:
        d += 1
        distances = new_distances
    else:
        break

distances = dict(distances.collect())
# print(distances)

path = []

prev_v = None
if target_found:
    path.append(str(target_found[0]))
    prev_v = target_found[1][1]

while prev_v:
    path.append(str(prev_v))
    prev_v = distances[prev_v][1]

print ','.join(path[::-1])
```


输出是：

```
12,422,53,52,107,20,23,274,34
```

 但是用大文件，就跑的非常慢，慢到怀疑人生。。。 在作业里面直接打印反而过了。。。

-
