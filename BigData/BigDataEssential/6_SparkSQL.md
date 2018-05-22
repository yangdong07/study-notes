

## SparkSQL


### DataFrame

与pandas的DataFrame 类似（ inspired by pandas dataframe）

可以互相转换。 但是注意 Spark的 Dataframe是 分布式的，数据不一定在本地。 而pandas的Dataframe全部在本地内存中。 如果数据量过大，会把内存撑爆。

### Spark and Hive

`enableHiveSupport()`

#### 查看Hive数据库的两种方式

Sql语句 `spark.session.sql`
- `show database` ，显示数据库
- `show tables in web`， 显示所有的表

SparkSession方法：

- `spark_session.catalog.listDatabases()`
- `spark_session.catalog.listTables('web')`

#### Spark View

类似于Hive的 View。 是在查看数据的时候才开始计算。lazy。

```python
geoip_df.createTempView("geoip")
spark_session.catalog.listTables("web") # 会显示这个新的 table，类型是 "TEMPORARY" 的
```

### RDD vs. DF vs. Sql

spark sql有三种方式：

1. RDD  API（python）
2. DataFrame API （会内部优化， scala语言）
3. SQL

第一种方式会比较慢，因为都是python代码。 第二、三种方式比第一种方法快上 2~ 5倍！

可以自定义udf：  `sprk_session.udf.register`

建议使用 DataFrame API， 还可以及早发现错误。

### Projection and Filtering

Spark DataFrame的一些操作跟 Pandas 很像。 可以只选择若干列，或者按条件筛选。

### Spark SQL Functions

`pyspark.sql.functions`

三种类型：

1. map， 1对1
2. generate， 生成若干。 经常见的就是 explode 函数，就是展开的意思。
3. aggregate， 聚合

这些函数非常灵活，可以从col里面生成新的col， 重新命名，等等。
