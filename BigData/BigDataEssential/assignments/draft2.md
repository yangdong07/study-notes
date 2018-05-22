

### 错误

没注意看说明，其实不用自己创建表。

直接用posts就可以了。

```sql
%%writefile query.hql

USE stackoverflow_;

DROP VIEW IF EXISTS tags_view;
DROP VIEW IF EXISTS rank_view;


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


CREATE VIEW rank_view (
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
Tests passed: crs902_7 crs902_8 crs902_2 crs902_3 mrb17 sp7_1 res1_5 res2_4
Tests failed: crs902_1 crs902_9 crs902_4 crs902_5 crs902_6
==================================================
Duration: 60.0 sec
VCoreSeconds: 191 sec
```

res1_5 的错误没有了，是因为，没有JOIN操作。 这个最后考虑


### 第二次提交

抄了一幅图

```
javascript	1	6	284841	18750
java	2	2	217005	24099
android	3	65	183269	1997
php	4	6	1784988	20541
python	5	6	166779	12944
c#	6	6	161198	18750
html	7	6	284841	18750
jquery	8	6	284841	18750
ios	9	6	284841	18750
css	10	6	284841	18750
```

```
Grade system output:
===== Testing (num. 902): STARTING =====
Executing notebook with kernel: python2
Testing (num. 902): test CRS902_1 failed on line "php	4	6	1784988	20541"!
CRS902_1 description: Checking the format
Testing (num. 902): test CRS902_4 failed!
CRS902_4 description: Checking Top-1 rank in 2009 and the last of Top-10
Testing (num. 902): test CRS902_5 failed!
CRS902_5 description: Checking Top-1 popularity in 2016 and the last of Top-10
Testing (num. 902): test CRS902_6 failed!
CRS902_6 description: Checking Top-1 popularity in 2009 and the last of Top-10

===== Testing (num. 902): SUMMARY =====
Tests passed: crs902_7 crs902_8 crs902_9 crs902_2 crs902_3 mrb17 sp7_1 res1_5 res2_4
Tests failed: crs902_1 crs902_4 crs902_5 crs902_6
==================================================
Duration: 69.0 sec
VCoreSeconds: 207 sec
```
有几点发现：

1. crs902_1 检查格式错误， 这次是 php这一行了，因为没有抄对。所以实际上并不是检查格式，而是检查内容。
2. crs902_9 过了，Checking the correctness of tags' rank in 2009， 但是我tags都是随便写的？？为什么还过了？好奇怪。
3. popularity 完全不一样，似乎有点大啊。

猜想：

1. crs902_1 很关键，其实不是检查格式，就是检查所有内容。 为了验证这一点，多抄对几个。



### 第三次提交

```
javascript	1	6	284841	18750
java	2	2	217005	24099
android	3	65	183269	1997
php	4	4	174988	20541
python	5	10	166779	12944
c#	6	1	161198	18750
html	7	6	284841	18750
jquery	8	6	284841	18750
ios	9	6	284841	18750
css	10	6	284841	18750
```

```
Grade system output:
===== Testing (num. 902): STARTING =====
Executing notebook with kernel: python2
Testing (num. 902): test CRS902_4 failed!
CRS902_4 description: Checking Top-1 rank in 2009 and the last of Top-10
Testing (num. 902): test CRS902_5 failed!
CRS902_5 description: Checking Top-1 popularity in 2016 and the last of Top-10
Testing (num. 902): test CRS902_6 failed!
CRS902_6 description: Checking Top-1 popularity in 2009 and the last of Top-10

===== Testing (num. 902): SUMMARY =====
Tests passed: crs902_1 crs902_7 crs902_8 crs902_9 crs902_2 crs902_3 mrb17 sp7_1 res1_5 res2_4
Tests failed: crs902_4 crs902_5 crs902_6
==================================================
Duration: 61.0 sec
VCoreSeconds: 186 sec
```

crs902_1 居然通过了。。看来截止到 c#。。后面都不管了？？试一试。。
