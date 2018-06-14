

## Hive Task

<https://www.coursera.org/learn/big-data-analysis/supplement/NHqVS/hive-assignment-intro>


### Task1

<https://www.coursera.org/learn/big-data-analysis/gradedLti/rJMpL/hive-assignment-task1>


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
一种类似于 tag， 或者xml文本的格式。

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

这里用到 `RegexSerDe` 来解析文件的每一行。 用到的正则表达式可以在 <https://regex101.com/r/l71G6B/1/> 进行测试。 这里注意 `\` 需要用 `\\`表示。 所以 `\d`需要写成 `\\d`

External Table 可以随意删除而不影响原始数据。

#### 3. 创建 Managed Partitioned 表，并注入数据

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

注意 Partitioned表的创建方法。 这是 Managed Table，所以Drop掉之后，数据也会跟着删掉。 注意 Partitioned Table的字段，只有一个 `id`。 其他字段（year、month）是以目录树的形式分区。

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

把 Location改成 `'/user/hjudge/posts_sample'` 就过了， fuck fuck fuck!


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



#### 参考 Eugene 的做法，一个语句搞定：

```python
%%writefile query.hql

USE stackoverflow_;

WITH tags_rank AS (
    SELECT
        year,
        lower(tag) as tag,
        COUNT(DISTINCT id) as popularity,
        RANK() OVER (PARTITION by year ORDER BY COUNT(DISTINCT id) DESC) as rank
    FROM posts
    LATERAL VIEW explode(tags) explode_tags as tag
    WHERE post_type_id=1 AND tags IS NOT NULL AND year IN (2009, 2016)
    GROUP BY year, lower(tag))
SELECT a.tag, a.rank, b.rank, a.popularity, b.popularity
FROM tags_rank a
LEFT OUTER JOIN
tags_rank b ON (a.tag=b.tag AND b.year=2009)
WHERE a.year=2016
ORDER BY a.popularity DESC
LIMIT 10;
```

大致思想： 

1. 第一步先取出来2009， 和2016 的数据，展开tag。可以发现在用LATERAL VIEW的时候，是可以做筛选的和 GROUP的，只不过是放在后面。
2. 第二步就是用这个table， 自己与自己 LEFT OUTER JOIN。非常赞。

测试与提交的输出不一样。 提交之后的输出是：

```
=== Task's top output ===
javascript	1	6	284841	18750
java	2	2	217005	24099
android	3	65	183269	1997
php	4	4	174988	20541
python	5	10	166779	12944
c#	6	1	161198	46143
html	7	12	123863	9375
jquery	8	8	121558	14513
ios	9	290	94659	511
css	10	19	84251	6798
```

### Task3

要求，这次是两个表：posts 和users

Calculate number of questions and answers depending on users' age. Use аge from 'users' table, filter out users if their age is undefined. Output format:
`age <tab> number of questions <tab> number of answers`

Output all ages. Order by age, increment.

Hint. To simplify your code and reduce the quantity of MapReduce jobs produced by the query, use IF clause.

 输出所有年龄的问题数量和回答数量

posts 和 users 可以通过 user id 联系起来， 做成一个 ： `id, post_type_id, age` 的表。 计数的时候，可以用 IF。

```sql
%%writefile query.hql

USE stackoverflow_;

SELECT
    users.age,
    SUM(IF(post_type_id=1, 1, 0)),
    SUM(IF(post_type_id=2, 1, 0))
FROM posts
JOIN users ON (posts.owner_user_id=users.id)
WHERE age IS NOT NULL
GROUP BY age
ORDER BY age;

```

输出：
```
=== Task's top output ===
13	799	271
14	1497	3382
15	4399	4996
16	7886	11746
17	12932	19209
18	17923	24105
19	28560	37042
20	41498	56715
21	63154	80272
22	87962	127698
```
