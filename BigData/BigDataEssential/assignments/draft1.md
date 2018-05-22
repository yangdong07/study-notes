
### HQL

```sql
%%writefile query.hql

ADD JAR /opt/cloudera/parcels/CDH/lib/hive/lib/hive-contrib.jar;
ADD JAR /opt/cloudera/parcels/CDH/lib/hive/lib/hive-serde.jar;

SET hive.exec.dynamic.partition.mode=nonstrict;

USE stackoverflow_;

DROP TABLE IF EXISTS posts_external;
DROP TABLE IF EXISTS posts;


CREATE EXTERNAL TABLE posts_external (
    id INT,
    post_type_id TINYINT,
    creation_year STRING,
    tags STRING,
    owner_user_id INT,
    parent_id INT,
    score INT,
    favorite_count INT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
    "input.regex" = ".*?(?=.*\\bId=\"(\\d+)\")(?=.*\\bPostTypeId=\"(\\d+)\")(?=.*\\bCreationDate=\"(\\d{4}))(?=.*\\bTags=\"([^\"]+)\")?(?=.*\\bOwnerUserId=\"(\\d+)\")?(?=.*\\bParentId=\"(\\d+)\")?(?=.*\\bScore=\"(-?\\d+)\")(?=.*\\bFavoriteCount=\"(\\d+)\")?.*"
)
STORED AS TEXTFILE
LOCATION '/data/stackexchange1000/posts';


CREATE TABLE posts (
    id INT,
    post_type_id TINYINT,
    owner_user_id INT,
    parent_id INT,
    score INT,
    favorite_count INT,
    tags ARRAY<STRING>
)
PARTITIONED BY (year STRING)
LOCATION '/user/hjudge/posts_samples';


INSERT OVERWRITE TABLE posts
PARTITION(year)
SELECT
    id,
    post_type_id,
    owner_user_id,
    parent_id INT,
    score INT,
    favorite_count INT,
    split(regexp_replace(regexp_replace(tags, "&lt\;", ""), "&gt\;$", ""), "&gt\;") AS tags,
    creation_year
FROM posts_external
WHERE id IS NOT NULL;


SELECT * FROM posts LIMIT 10;
```

```sql
%%writefile query2.hql

USE stackoverflow_;

DROP VIEW IF EXISTS tags_view;
DROP VIEW IF EXISTS tags_rank_view;


CREATE VIEW tags_view (
    year,
    tag,
    popularity
)
AS SELECT year, tag, COUNT(*) as popularity
FROM (
    SELECT year, tags
    FROM posts
    WHERE post_type_id=1) AS questions
LATERAL VIEW explode(tags) explode_tags AS tag
GROUP BY year, tag;


CREATE VIEW tags_rank_view (
    year,
    tag,
    rank,
    popularity)
AS SELECT
    year,
    tag,
    RANK() OVER (PARTITION BY year ORDER BY popularity DESC) AS rank,
    popularity
FROM tags_view;


SELECT tag, rank, rank, popularity, popularity
FROM tags_rank_view
WHERE year="2016"
ORDER BY rank LIMIT 10;
```


### 第一次提交

```
javascript 1 7 341 19
android 2 NULL 236 NULL
java 3 2 226 28
python 4 7 216 19
php 5 4 209 24
c# 6 1 183 45
jquery 7 5 156 20
html 8 14 143 9
ios 9 127 118 1
c++ 10 16 91 8
```
```
Your score is 0.

Grade system output: 
===== Testing (num. 902): STARTING =====
Executing notebook with kernel: python2
Testing (num. 902): test RES1_5 failed!
RES1_5 description: All jobs but one should has at least 1 reducer
Testing (num. 902): test CRS902_1 failed on line "javascript 1 7 341 19"!
CRS902_1 description: Checking the format
Testing (num. 902): test CRS902_9 failed on line "javascript 1 7 341 19"!
CRS902_9 description: Checking the correctness of tags' rank in 2009
Testing (num. 902): test CRS902_7 failed on line "c++ 10 16 91 8"!
CRS902_7 description: Checking the correctness of tags
Testing (num. 902): test CRS902_2 failed!
CRS902_2 description: Checking Top-1 tag and the last of Top-10
Testing (num. 902): test CRS902_4 failed!
CRS902_4 description: Checking Top-1 rank in 2009 and the last of Top-10
Testing (num. 902): test CRS902_5 failed!
CRS902_5 description: Checking Top-1 popularity in 2016 and the last of Top-10
Testing (num. 902): test CRS902_6 failed!
CRS902_6 description: Checking Top-1 popularity in 2009 and the last of Top-10

===== Testing (num. 902): SUMMARY =====
Tests passed: crs902_8 crs902_3 mrb17 sp7_1 res2_4
Tests failed: res1_5 crs902_1 crs902_9 crs902_7 crs902_2 crs902_4 crs902_5 crs902_6
==================================================
Duration: 158.0 sec
VCoreSeconds: 2703 sec 
```

主要是：
- crs902_1，检查格式：格式错误
- crs902_2，检查第1行 和第10行的tag： 错误
- crs902_3，不知道检查什么，但是是对的（根据上下文推理，可以知道应该是检查 rank in 2016）
- crs902_4，检查第1行和第10行 rank in 2009： 错误
- crs902_5，检查第1行和第10行 popularity in 2016 是错误的
- crs902_6，检查第1行和第10行 popularity in 2009 是错误的
- crs902_7，检查所有tags， 似乎是C++行，最后一行错误。
- crs902_8，正确，推测是检查所有 rank in 2016，这个不管怎样都是正确的。
- crs902_9，检查所有 rank in 2009，第一行错误。

主要：
- 格式问题
- crs902_7， 看看是不是就是最后一个tag的问题，还是其他tag都有问题。
- 数字问题

可以测试一下 crs902_7， 随便写几个上去


### 第二次提交

尝试用 '/data/stackexchange100/posts'， 并且只输出 2016年的rank。 2009的数据肯定是错误的，看看有哪些是错误的。

```
javascript	1	1	2771	2771
java	2	2	2033	2033
android	3	3	1809	1809
php	4	4	1673	1673
python	5	5	1585	1585
c#	6	6	1519	1519
html	7	7	1212	1212
jquery	8	8	1167	1167
ios	9	9	914	914
css	10	10	801	801
```

```
Grade system output:
===== Testing (num. 902): STARTING =====
Executing notebook with kernel: python2
Testing (num. 902): test RES1_5 failed!
RES1_5 description: All jobs but one should has at least 1 reducer
Testing (num. 902): test CRS902_1 failed on line "javascript	1	1	2771	2771"!
CRS902_1 description: Checking the format
Testing (num. 902): test CRS902_9 failed on line "android	3	3	1809	1809"!
CRS902_9 description: Checking the correctness of tags' rank in 2009
Testing (num. 902): test CRS902_4 failed!
CRS902_4 description: Checking Top-1 rank in 2009 and the last of Top-10
Testing (num. 902): test CRS902_5 failed!
CRS902_5 description: Checking Top-1 popularity in 2016 and the last of Top-10
Testing (num. 902): test CRS902_6 failed!
CRS902_6 description: Checking Top-1 popularity in 2009 and the last of Top-10

===== Testing (num. 902): SUMMARY =====
Tests passed: crs902_7 crs902_8 crs902_2 crs902_3 mrb17 sp7_1 res2_4
Tests failed: res1_5 crs902_1 crs902_9 crs902_4 crs902_5 crs902_6
==================================================
Duration: 130.0 sec
VCoreSeconds: 561 sec
```

错误：
- res1_5， All jobs but one should has at least 1 reducer， 只有一个需要至少一个 reducer。意思是其他都不需要？
- crs902_1，检查格式：格式仍然错误，为什么？
- crs902_2，检查第1行 和第10行的tag： 正确了！！！ 果然还是用 100。题目都没说清楚，真是服了。
- crs902_3，不知道检查什么，但是是对的（根据上下文推理，可以知道应该是检查 rank in 2016）
- crs902_4，检查第1行和第10行 rank in 2009： 肯定是错误的
- crs902_5，检查第1行和第10行 popularity in 2016 是错误的
- crs902_6，检查第1行和第10行 popularity in 2009 是错误的
- crs902_7，检查所有tags， 通过了，说明这个所有tags是正确的！！！！！
- crs902_8，正确，推测是检查所有 rank in 2016，这个不管怎样都是正确的。
- crs902_9，检查所有 rank in 2009，第一行错误。

crs902_7 检查所有tags是正确的，这就说明 用stackexchange100 而不是用 stackexchange1000。

res1_5 问题： 前面partition 都只是map。 UDAF似乎也需要 reducer。 JOIN操作肯定要用到 reducer。 这个问题最后再说。

做一些改动，试图解决 rank in 2009问题


### 第三次提交

Hello, can anyone help with hive assignment task 2, i’m going mad! I got the answer
```
javascript	1	5	2771	192
java	2	2	2033	243
android	3	52	1809	25
php	4	3	1673	215
python	5	11	1585	108
c#	6	1	1519	423
html	7	14	1212	84
jquery	8	8	1167	141
ios	9	186	914	7
css	10	20	801	59
```

and the grader output
```
Grade system output:
===== Testing (num. 902): STARTING =====
Executing notebook with kernel: python2
Testing (num. 902): test RES1_5 failed!
RES1_5 description: All jobs but one should has at least 1 reducer
Testing (num. 902): test CRS902_1 failed on line "javascript	1	5	2771	192"!
CRS902_1 description: Checking the format
Testing (num. 902): test CRS902_9 failed on line "javascript	1	5	2771	192"!
CRS902_9 description: Checking the correctness of tags' rank in 2009
Testing (num. 902): test CRS902_4 failed!
CRS902_4 description: Checking Top-1 rank in 2009 and the last of Top-10
Testing (num. 902): test CRS902_5 failed!
CRS902_5 description: Checking Top-1 popularity in 2016 and the last of Top-10
Testing (num. 902): test CRS902_6 failed!
CRS902_6 description: Checking Top-1 popularity in 2009 and the last of Top-10

===== Testing (num. 902): SUMMARY =====
Tests passed: crs902_7 crs902_8 crs902_2 crs902_3 mrb17 sp7_1 res2_4
Tests failed: res1_5 crs902_1 crs902_9 crs902_4 crs902_5 crs902_6
==================================================
Duration: 206.0 sec
VCoreSeconds: 872 sec
```
the tags and rank in 2016 is right, but all the others is wrong, the popularity is not the right answer, but i do not known why. Can anyone help?



错误
 - crs902_4，检查第1行和第10行 rank in 2009： 肯定是错误的
 - crs902_5，检查第1行和第10行 popularity in 2016 是错误的
 - crs902_6，检查第1行和第10行 popularity in 2009 是错误的

crs902_9 检查所有rank in 2009， 这个不一样： "javascript	1	5	2771	192" 与上一次提交 "android	3	3	1809	1809" 不一样

确定的信息是：
- tags in 2016 按照rank排序是正确的， popularity in 2016的顺序是正确的。
- 但是popularity in 2016 的数值有问题
- tags in 2009的rank，  javascript 和 java 都是 1， 2。 从android开始是错误的。




输出
```
javascript	1	341	1	341
android	2	236	2	236
java	3	226	3	226
python	4	216	4	216
php	5	209	5	209
c#	6	183	6	183
jquery	7	156	7	156
html	8	143	8	143
ios	9	118	9	118
c++	10	91	10	91
```
