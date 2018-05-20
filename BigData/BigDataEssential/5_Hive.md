

## Hive

一句话表示Hive： Hive 定义 HQL（类似SQL的） 操作 HDFS文件数据。 Hive并不重复数据，而是组织管理数据。

### 基本概念


- [Hive](https://hive.apache.org/), The Apache Hive ™ data warehouse software facilitates reading, writing, and managing large datasets residing in distributed storage using SQL. Structure can be projected onto data already in storage. A command line tool and JDBC driver are provided to connect users to Hive.

- Hive vs. Pig ：二者都是基于 HDFS和MapReduce框架。  Hive是 declarative（what）声明式的， ~SQL。 Pig是 procedural（how），过程式的。 没有说可以解决所有问题的方案。 视问题使用，选择合适的解决方案。现在看来， Hive 比 Pig流行一些。

- 问题域： 一个常见的大数据处理场景是 处理访问日志(web-service access logs)，包括了用户的访问记录，可以分析用户的分布、热度等等信息。


- SQL基本操作：
    - extract data: select
    - filter and order data: where, order by
    - aggregate data: group by, sum, avg, min, max, having(filter of group aggregate)
    - insert
    - delete
    - update
    - join data:  inner, left, right, outer
    - use subqueries

- DDL: Data Definition Language. 我们说 Language，是一种符号概念，某个符号表示什么意思。 数据定义语言说明了数据库系统所使用的存储结构和访问方式。比如定义约束。
- DML: Data Manipulation Language. 数据操纵语言，是一套描述如何访问和操纵数据的语言。
    - 过程式 DML（procedural DML）：要求用户指定需要什么数据以及如何获得这些数据
    - 声明式 DML（declarative DML）： 要求用户指定需要什么数据，而不指明如何获得这些数据。

- QL: DML中涉及信息检索的部分称作查询语言（query language）。通常将QL和DML作为同义词使用。

### Hive DDL

- Hive Metastore: Hive 存储的是数据的 metadata （定义结构）。**实际数据在HDFS中**，是分布式的。注意Namenode记录的meta信息只是文件级别的。 Hive存储的是数据（内容）级别的meta信息。

- Database： 如果不指名 database，则使用 'default' 作为database
- Create Table： 就像普通创建表一样，定义数据结构。同时要注明数据位置： LOACTION
- Table Types
    - Managed table： hive负责管理数据。 **如果drop table， 则会把数据也删掉！！！！**
    - External table： 没有这些负担！
- Delimiters: 不是常见的 '\t'， 而是不常见的 '\001'（'^A')，
    - '\001', '^A', FIELDS分隔符
    - '\002', '^B', COLLECTIONS 分隔符， array结构各元素分隔符，或者struct的分隔符
    - '\003', '^C', MAP 键值分隔符， 字典键与值之间的分隔符, 键值对之间用 '^B'
    - '\n'， 行分隔符
    - 如果数据格式不是这样，需要在DDL中指明 delimiter

```sql
CREATE EXTERNAL TABLE test {
    first_column  STRING,
    second_column  STRING,
    value  INT
}
ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\001'
    COLLECTION ITEMS TERMINATED BY '\002'
    MAP KEYS TERMINATED BY '\003'
    LINES TERMINATED BY '\n'

LOCATION '/path/to/data/'
```

- [Data types](https://dzone.com/articles/hive-data-types):
    - primitive: int, string, boolean, etc
    - complex:  array, map, struct, union


### Hive DML

- import data
    - `LOAD DATA INPATH '/hdfs/path/to/data' INOT TABLE employees;`  这里 employees已经定义过了，并且有location。 这个操作就是将另外的数据也放到这个location下面，类似于 `hdfs dfs -mv`
    - `LOAD DATA LOCAL INPATH ...` 这里多了LOCAL关键字，就是将本地文件放入 hdfs系统，类似 `hdfs dfs -put`
    - `LOAD DATA [LOCAL] INPATH '...' OVERWRITE INTO TABLE xxx;` 就是将原来的location里面的文件都给干掉，替换成新的。

- schema on read： 与传统数据库不同。 传统数据库 schema on write，是在加载（LOAD） data的时候就检查 data是否符合 schema。 如果不符合则报错。 Hive 加载就是加载，不检查。 只有在读数据的时候再检查 schema，如果错了就会报错。 所以用户负责数据格式的正确性。

- export data
```sql
INSERT OVERWRITE [LOCAL] DIRECTORY '/tmp/employees'
SELECT * FROM xxx WHERE ...
```

- export data, multiple-insert statement，同时插入多个文件。
```sql
FROM employees
INSERT OVERWRITE [LOCAL] DIRECTORY '/tmp/ca_employees'
SELECT *  
WHERE state = 'CA'
INSERT OVERWRITE [LOCAL] DIRECTORY '/tmp/ny_employees'
SELECT *  
WHERE state = 'NY'
```

- 换个姿势操作，甚至可以同时创建多个table， 注意这里实际的HDFS数据并没有移动、复制。创建的只是描述结构。
```sql
FROM raw_table
INSERT OVERWRITE TABLE ca_employees
SELECT *  
WHERE state = 'CA'
INSERT OVERWRITE TABLE ny_employees
SELECT *  
WHERE state = 'NY'
```

- DDL(CTAS)： Create Table As Select。 这里也没有产生实际的数据复制、移动。 LOCATION是相同的。似乎是封装了一层。
```sql
CREATE TABLE ca_employees
AS SELECT *
FROM employees
WHERE state = 'CA'
```

- Hive QL : MapReduce
    - select .. from   :  map
    - where :   map
    - group by:    shuffle & sort  
    - having:   reduce
    - join:   map side / reduce side
    - order by/ sort by  : reduce

- ORDER BY vs. SORT BY，  ORDER BY 是全排序， 比较耗费资源。 SORT BY 是文件内排序。是 partial sort。


### Regex SerDe

`/^[Reg]ular[Ex]pression$/`

SerDe：  Serialization + Deserialization

创建 TABLE，定义schema的另一种方式

例如log形式：

`127.0.0.1 - frank [10/Oct/2000:13:44:45 -0700] "GET /apache_pb.gif HTTP/1.0" 200`

```sql
CREATE EXTERNAL TABLE apache_log (
    ip  string,
    auth_unused string,
    auth_user string,
    request_time string,
    request string,
    status_code int
)
ROW FORMAT
    SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
    WITH SERDEPROPERTIES (
        "input.regex" = '^(\\S*) (\\S*) (\\S*) \\[([^\\]]*)\\] "([^"]*)" (\\S*) .*$'
    )
LOCATION '/path/to/log';
```

关于regex的几个小点：

1. `\\` 表示 `\`
2. `\S` 表示非空白字符
3. `[^\\]]*` 表示除了 `]` 字符的其他字符。
4. `[^"]*` 表示除了 `"` 字符的其他字符。

这样就把每行输入解析出来了。


### View

View 是建立在已有的Table基础之上的，是只读的（不可写）。 View可以使用 regex 进一步解析字符串。

```SQL
CREATE VIEW apache_log_view (
    ip,
    request_year,
    request,
    status_code
)
AS SELECT
    ip,
    regexp_extract(request_time, "\\d+\\/\\w+\\/(\\d+)", 1)
    request, status_code
FROM apache_log;
```

VIEW 有一些缺点： 它是只读的，并且建立在已有TABLE之上。如果 TABLE的结构发生了变化，VIEW不会自己更新，再访问时会出现错误。

### 复习： regex in python

- match vs. search ， 前者从头匹配，后者搜索第一个匹配
- `re.IGNORECASE`
- Capture Group， 用 `()` 捕获组
- group vs. groups 参考 https://docs.python.org/3/library/re.html#re.match.group。 group 是所有匹配的，包括组。 group(0) 是匹配的全部字符； group(1) 是第一个捕获的组； groups 则仅包括所有捕获的组。所以 group比groups还多一些。
- Non-Capturing Group 不要捕获的组 :  `(?:...)`，不会出现在 group或者groups里。
- `[]`里面表示字符选项，例如 `[a-zA-Z0-9_]`
- `*+?` 分别表示 0或多个； 1或多个； 0或1个。
- `{n, m}` 表示 n到m个。

- metacharacters:  `\d` 数字
- `\w` 字母数字
- `\s` 空白字符
- `[^x]` 表示一切不是 `x` 的字符
- `^$` 分别表示 开头和结尾。


### Hive Functions

- Operators
- UDFs: User Defined Functions,  1: 1, 类似于 MAP，一对一映射
- UDAFs: User Defined Aggregate Functions,  n: 1， REDUCE， 聚合操作
- UDTFs: User Defined Table-generating Functions,  1: n，  产生表的操作 （例如类似flatMap这种）


不清楚函数功能：

- `describe function xxx;`
- `describe function extended xxx;` 详细


开发流程：

1. develop UD[.\*]F
2. compile to \*.jar
3. deploy to cluster

`hive > add jar /path/to/lib.jar;`  place into distributed cache

如果要用自定义的 UD[.\*]F，需要创建临时函数，例如

```
hive> add jar /path/to/lib.jar
hive> create temporary function func_name as "java.class.name"
hive> select func_name(...) ...;
...
hive> drop temporary function func_name;
```

或者持久函数：
```
hive> create function [db_name.]func_name as "java.class.name"
[USING JAR "/path/to/lib.jar"];
hive> select func_name(...) ...;
...
hive> drop temporary function func_name;
```



### LATERAL VIEW

参考 <https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView>

有点像 flatMap的操作： 把一行中的 list展开成 若干行。 它使用 UDTF 函数 explode。 例如：

表： pageAds
|pageid | adid_list|
|--|--|
|front_page| [1, 2, 3]|
|contact_page | [3, 4, 5]|


```sql
SELECT pageid, adid
FROM pageAds LATERAL VIEW explode(adid_list) adTable AS adid;
```

会产生一个新表： adTable
|pageid| adid|
|---|---|
|"front_page"| 1|
|"front_page"| 2
|"front_page"| 3
|"contact_page"| 3
|"contact_page"| 4
|"contact_page"| 5


可以用 EXPLAIN 命令观察 HDL语句是如何分解成 stages的。


## Hive Streaming

就像 Hadoop Streaming一样，也可以用 Hive Streaming，使用外部的python脚本，做一些工作。

```sql
FROM my_table
SELECT TRANSFORM (column_A, column_B)
USING "/bin/cat"
AS (new_A STRING, new_B DOUBLE)
```

就是将 `column_A`  `column_B` 经过脚本命令，变成新的列。


例如之前的 word count任务：

1. map
2. shuffle & sort， 以word为key
3. reducer

```sql
FROM (
  FROM wikipedia_sample
  SELECT TRANSFORM (line)
  USING "python mapper.py" AS word, counts
  DISTRIBUTE BY word SORT BY word
) word_pairs
SELECT TRANSFORM (word_pairs.word, word_pairs.counts)
USING "python reducer.py"
AS word, counts
```

注意这里面的 `DISTRIBUTE BY word SORT BY word` 做了 shuffle-> partition, sort in partition的工作。 也可以用 `CLUSTER BY` 替代。 但是注意不要直接用 `MAP` 和 `REDUCE`命令。 hive支持，但是这样做很危险。不明白为什么。

将 py文件添加到hive（Distributed Cache）：

`hive> ADD FILE /path/to/xxx.py`


## Hive PTF

Partitioned table Functions

```sql
SELECT
    customerID,
    transactionID,
    change,
    SUM(change) OVER (
        PARTITION BY customerID
        ORDER BY transactionID
    )
FROM transactions
SORT BY customerID, transactionID;
```

上面这个操作，就是按照 customerID 进行分区， 然后在这个分区上按照 transactionID排序， 然后对change做累加操作。

最后注意这个 SORT BY 是在分区内部的。  括号里面的ORDER BY也是分区内部的。



## Hive Optimization

### Partitioning

问题： 如果一种操作需要对全部HDFS文件进行访问，这样会非常耗费资源，尽量避免这样做

Partitioning是一种方法。

关于partition操作，参考 <https://resources.zaloni.com/blog/partitioning-in-hive> 写的比较多，但是都提到了。

有几个点注意一下：

1. partition 是一种真实的数据组织方式： 将文件按照某些column的值分别存放。 例如按照年月日： `/year/month/day/file` 存放。
2. 可以对partition改名。
3. 一个问题是： partitioned操作什么时候创建路径和文件？

```sql
FROM raw_access_log
INSERT OVERWRITE TABLE partitioned_access_log
PARTITION (year, month, day)
SELECT ip, ..., year, month, day
```

注意
1.  partition的 columns一定要在select的最后面。

有静态的和动态的两种。 需要实际操作和观察。还有一些配置。

更多参考 ：

- <https://cwiki.apache.org/confluence/display/Hive/DynamicPartitions>



### 关于  CLUSTER BY / ORDER BY / SORT BY/ DISTRIBUTE BY

参考 <https://stackoverflow.com/questions/13715044/hive-cluster-by-vs-order-by-vs-sort-by> 回答的很好：

- ORDER BY x: guarantees global ordering, but does this by pushing all data through just one reducer. This is basically unacceptable for large datasets. You end up one sorted file as output.
- SORT BY x: orders data at each of N reducers, but each reducer can receive overlapping ranges of data. You end up with N or more sorted files with overlapping ranges.
- DISTRIBUTE BY x: ensures each of N reducers gets non-overlapping ranges of x, but doesn't sort the output of each reducer. You end up with N or unsorted files with non-overlapping ranges.
- CLUSTER BY x: ensures each of N reducers gets non-overlapping ranges, then sorts by those ranges at the reducers. This gives you global ordering, and is the same as doing (DISTRIBUTE BY x and SORT BY x). You end up with N or more sorted files with non-overlapping ranges.

简单说就是：

- ORDER BY： 全局排序，放到一个reducer里。
- SORT BY： 在partition内部排序，局部排序。 但是全局的顺序不一定。每个partition的范围有重叠。
- DISTRIBUTE BY： 每个partition的范围没有重叠， 但是内部是无序的。
- CLUSTER BY： 每个partition的范围没有重叠，且内部有序，这就等同于 DISTRIBUTE BY + SORT BY


### Bucketing and Sampling

在partition基础上，还可以将文件细分。 类似于 hdfs的  part-* 文件。

参考 <https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL+BucketedTables>

这是一个新的 table
```sql
CREATE TABLE user_info_bucketed(user_id BIGINT, firstname STRING, lastname STRING)
COMMENT 'A bucketed copy of user_info'
PARTITIONED BY(ds STRING)
CLUSTERED BY(user_id) INTO 256 BUCKETS;
```

注入数据：
```sql
set hive.enforce.bucketing = true;  -- (Note: Not needed in Hive 2.x onward)
FROM user_id
INSERT OVERWRITE TABLE user_info_bucketed
PARTITION (ds='2009-02-25')
SELECT userid, firstname, lastname WHERE ds='2009-02-25';
```

这样 文件就基于  user_id 分割成256个 buckets了。


使用bucketing之后，还可以在此基础上做 sampling。


### Joins

也没怎么看懂。 这里主要是map side join。 两个点：

1. 数据分 bucket， 按照bucket join。
2. extenal merge sort。

### Data Skew

完全没看懂。hive可以支持DataSkew。定义某个 key 是 data skew的。

通常的处理方式（之前学过）： 比如有大量null值， 在后面附上随机数字，会进入到随机分区中去，这样就均衡了数据分布。


### File Formats and Compression

这里主要讲了一下 Record Columnar File （RCFile）。 通常数据都是按行排列的。但是也可以用 列表示。这种方式相同数据类型的数据都放在一起，非常便于压缩。

例如： 日期， 其实可以用 timestamp表示，每个timestamp如果用字符，需要 10个bytes； 如果用数字，只需要 4个bytes； 甚至还可以用 差值，比如以表第一个日期为初始值，后面都是此基础之上的差值，则可以用一个bytes！


RCFile 的设计目标（Design Goals）

1. fast data loading，  快速数据加载？？
2. fast query processing， 有的查询只按列进行，所以一列数据放在一起就非常方便。
3. highly efficient storage space utilization， 这个能有效减少存储空间。
4. strong adaptivity to highly dynamic workload patterns


一些 RCFile：

ORC： Optimised Row Columnar

```sql
CREATE TABLE my_orc_table(
    ...
)
SORTED AS orc
    TBLPROPERTIES ("orc.compress" = "NONE")
...;
```



## Assignments

### Task1

要求：

1. 创建一个 database： stackoverflow， and use it
2. 创建一个 external table 'posts_sample_external'
3. 创建一个 managed table 'posts_sample'， 并且按照 post 的create date 的 year/month 分区。
4. query 列出各个分区的行数，格式 : `year <tab> month <tab> lines_count`

文件位置：`/data/stackexchange1000`， 文件格式：

```
reporter:status:Reading 	
<row Id="371392" PostTypeId="1" AcceptedAnswerId="372312" CreationDate="2008-12-16T14:08:55.857" Score="2" ViewCount="11027" Body="..." Tags="..." AnswerCount="1" CommentCount="0" FavoriteCount="4" />
```
一种类似于 tag， 或者xml文本的格式。

#### 1. 准备database
查看表
```
! hive --database stackoverflow_ -e 'show tables'
```

基本操作。
```sql
USE stackoverflow_;

SELECT * FROM posts LIMIT 10;
SELECT * FROM users LIMIT 10;
```

#### 2. 创建External表

```sql
ADD JAR /opt/cloudera/parcels/CDH/lib/hive/lib/hive-contrib.jar;
ADD JAR /opt/cloudera/parcels/CDH/lib/hive/lib/hive-serde.jar;

USE stackoverflow_;

DROP TABLE if exists posts_sample_external;

CREATE EXTERNAL TABLE posts_sample_external(
    id    STRING,
    month STRING,
    year  STRING   
)
ROW FORMAT SERDE
    'org.apache.hadoop.hive.serde2.RegexSerDe'
    WITH SERDEPROPERTIES (
        'input.regex' = '^.*Id="([^"]*)".*CreationDate="((\\d{4})-\\d{2}).*$' )
LOCATION '/data/stackexchange1000/posts'
TBLPROPERTIES ("skip.header.line.count"="1");

SELECT * from posts_sample_external LIMIT 10;
```

这里用到 `RegexSerDe` 来解析文件的每一行。 用到的正则表达式可以在 <https://regex101.com/r/l71G6B/1/> 进行测试。 这里注意 `\` 需要用 `\\`表示。 所以 `\d`需要写成 `\\d`

External Table 可以随意删除而不影响原始数据。

#### 3. 创建 Managed Partitioned 表，并注入数据

```sql
USE stackoverflow_;

DROP TABLE IF EXISTS posts_sample;

CREATE TABLE posts_sample(
    id    STRING)
PARTITIONED BY (
    year  STRING,
    month STRING)
LOCATION '/user/hjudge/posts_sample';

SET hive.exec.dynamic.partition.mode=nonstrict;
SET hive.exec.max.dynamic.partitions=2048;
SET hive.exec.max.dynamic.partitions.pernode=256;
SET hive.exec.max.created.files=10000;

FROM posts_sample_external
INSERT OVERWRITE TABLE posts_sample
PARTITION (year, month)
SELECT id, year, month;

SELECT * from posts_sample LIMIT 10;
```

注意 Partitioned表的创建方法。 这是 Managed Table，所以Drop掉之后，数据也会跟着删掉。 注意 Partitioned Table的字段，只有一个 `id`。 其他字段（year、month）是以目录树的形式分区。

可以从 `posts_sample_external` 查询结果注入数据。 这些查询操作会用到 MapReduce过程。


#### 4. 查询表格

题目要求很奇怪： 选择第三行输出。正常来说，如果要查看计数信息，一个`SELECT COUNT(*) FROM ... GROUP BY ...`就搞定了。但是还要选择第3行，所以加上 ROW_NUMBER信息，再 SELECT 一次就可以了。

如果需要行数信息，可以参考 <https://cwiki.apache.org/confluence/display/Hive/LanguageManual+WindowingAndAnalytics#LanguageManualWindowingAndAnalytics-Distinctcountingforeachpartition> ，使用 `ROW_NUMBER` 可以获取行号。

```sql
ADD JAR /opt/cloudera/parcels/CDH/lib/hive/lib/hive-contrib.jar;
ADD JAR /opt/cloudera/parcels/CDH/lib/hive/lib/hive-serde.jar;

USE stackoverflow_;

SELECT year, month, cnt FROM (
    SELECT
        COUNT(*) AS cnt,
        year, month,
        ROW_NUMBER() OVER (ORDER BY year, month ASC) AS rowid
    FROM posts_sample
    GROUP BY year, month) AS tmp
WHERE rowid='3';
```


#### fucking grader system

测试了好几遍答案是对的，但是提交确没有输出。

折腾了半天不知道原因，最后一步一步测试，发现是用户权限不同： 测试的时候用户是 fucking `javyan`， 提交的时候用户是 fucking `hjudge`。 提交创建 Managed Table的时候， Location的权限有问题， 是写不进去数据的。 所以最后的查询没有输出。

把 Location改成 `'/user/hjudge/posts_sample'` 就过了， fuck fuck fuck!


### Task2

要求：列出 2016年最流行的 tag， top-10， 并且与 2009 年的作比较。

注意几点：

1. tag流行度是指 问题的tag的计数统计； 问题是指 `post_type_id=1` 的 posts
2. 输出格式：  `tag <tab> rank in 2016 <tab> rank in 2009 <tag> tag popularity in 2016 <tag> tag popularity in 2009` ， 例如： `php 5 3 1234 5678`

大致的思路：

1. 需要解析出： `PostTypeId`, `year`, `tags` 信息
1. 因为 tag是列表， 需要将 tag展开
2. select 2016, group by tag and count tag, rank by tag count desc, select top 10.
3. select 2019, group by tag and count tag
4. 2016 top 10 left join 2019

直接做。




- ·
