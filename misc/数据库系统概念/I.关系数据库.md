## 第一部分： 关系数据库

主要介绍 关系模型，和 SQL。

## 第二章 关系模型介绍

- 表（table）/ 关系 （relation）： 表的一行表示一组值之间的一种联系，一个表就是这种联系的一个集合。 table 概念在数学上和 relation 是密切相关的。
- 元组（tuple）： 行
- 属性（attribute）： 列
- 关系实例（relation instance）： 表示一个关系（表）的特定实例，也就是所包含的一组特定的行。
- 域：关系的每个属性的取值范围（集合）
- 原子域：取值不可分割。 例如不能是列表。一个人的某个属性是A和B，其属性写成 [A, B]，就不是原子域
- 空值（null）

### 数据库模式

- 数据库模式（database schema）： 数据库的逻辑设计
- 数据库实例（database instance）： 给定时刻数据库中数据的一个快照
- 关系（relation） --- 程序设计语言中 变量的概念；
- 关系模式（relation schema）--- 类型定义的概念。 好理解，数据库模式就是定义数据结构、约束等等。
- 关系实例（relation instance） --- 变量的值。

### 码（key？？），键

- 一个关系中没有两个元组在所有属性上的取值都相同： 不允许出现两条完全一模一样的记录。
- 超键（superkey）： 是**一个或多个属性的集合**，这些属性的组合可以使我们在一个关系中唯一的确定一个元组。
- 候选键（candidate key）： 超键的任意真子集都不能成为超键，这样的最小超键称为候选键。比如 {A, B} 是超键，但是只凭 {A} 或 {B} 不可以唯一确定一个元组（分开来允许重复），则 {A, B}就是一个候选键。
- 主键（primary key）： 有设计者决定，用于区分不同元组的候选键。
- 外键（foreign key）： 一个关系模式（r1）的一个属性是另一个关系模式（r2）的主键， 则这个属性在 r1 上称做参照 r2 的外键。
- 参照关系（referencing relation）： r1 也称为外键依赖的参照关系。
- 被参照关系（referenced relation）： r2 称为外键的被参照关系。
- 参照完整性约束：要求参照关系中的任意元组在特定属性上的取值必然等于被参照关系某个元组在特定属性上的取值： 也就是说，这个值必须在被参照关系中存在。


### 其他

- 模式图（schema diagram）：数据库中模式的图形化表示，它显示了数据库中的关系，关系的属性、主码和外码。
- 关系查询语言（relational query language）
- 关系代数（relational algebra）
- 关系运算
    - select，选择（筛选）： 返回输入关系中满足谓词的行
    - projection，投影： 对输入关系的所有行输出指定的属性
    - 自然连接： join操作，在某个属性上值相同的元组连接在一起
    - 笛卡尔积： `[1, 2] x [3, 4] = [(1, 3), (1, 4), (2, 3), (2, 4)]`
    - 并： 输出两个输入关系中元组的并。


## 第三章 SQL

SQL 是最有影响力的商用市场化的关系查询语言。SQL语言包括几个部分：

- DDL，提供了定义关系模式、删除关系以及修改关系模式的命令
- DML，包括查询语言，以及往数据库中插入元组、从数据库中删除元组和修改数据库中元组的命令。


回顾一下，关系模型的几个概念： 关系、元组、模式、实例。 属性。

### SQL 数据定义

```
create table r
    (A1 D1,
    A2 D2,
    ...,
    An Dn,
    < 完整性约束1 >,
    ...,
    < 完整性约束k >);
```

`A` 表示 属性； `D` 是属性的域（类型和约束），限制了 A取值的集合。

一些常见约束：

- 主码约束（primary key）
- 外码约束（foreign key）
- 非空约束（not null）


SQL 禁止破坏完整性约束的任何数据库更新。


- 插入命令： `insert into r values ();`
- 删除元组： `delete from r;`
- 删除关系： `drop table r;`
- 增加属性： `alter table r add A D;`
- 删除属性： `alter table r drop A;`

### SQL 查询

`select from where`

#### from 子句

从哪个关系查询。可以是一个，也可以是多个关系的笛卡尔乘积，也可以是多个关系的自然连接（natural join）。 也可以是一个子查询。

#### where 子句

从from得到的关系，筛选出满足谓词条件的元组。

- 比较运算符：  `<, >, <=, >=, <>`
- 逻辑运算符：  `and, or`
- 其他： `in, not in`
- 其他的其他： `exists, not exists`，检查子查询是否存在

#### select 子句

虽然是最开始写，但实际上是最后一步执行，将查询得到的元组集合选择几个属性（投影）


### 其他基本运算

- as子句
    - 属性别名
    - 关系别名，表别名（table alias）、相关名称（correlation name）、相关变量（correlation variable）、元组变量（tuple variable）
    - 一个作用是简写；另一个作用是为了对一个关系进行两种操作而加以区分。

- like， 字符串，模式匹配
    - `%` 匹配任意子串
    - `_` 匹配任意 1 个字符
    - `\` 转义字符

- order by
    - asc 默认升序
    - desc， 降序

- between， 介于二者之间（包括边界）


### 集合运算

- union， 将两个查询结果关系（元组集合）并集，会自动去重
- union all， 会保留重复
- intersect， 交集，会自动去重
- intersect all， 保留重复，但是会保留重复最少的那个
    - 比如 {A, A, A, A} intersect all {A, A}，结果是 {A, A}
- except， 差运算，会自动去重
- except all， 保留重复


### 空值

SQL将 涉及空值的任何比较运算的结果视为 unknown（既不是谓词 `is null`，也不是 `is not null`

- and,  除了 false 与unknown 为 false 以外，其他都是unknown。
- or， 除了 true 与unknown 为true外，其他都是unknown
- not unknown = unknown


### 聚集函数

- avg
- min
- max
- sum
- count

#### 分组聚集

- group by
    - 如果不用group by，使用聚集函数，则默认将整个关系当做一个分组。

#### having 子句

有时候，对分组限定条件比对元组限定条件更有用

having子句中的谓词在形成分组后才起作用。

#### 对空值和布尔值的聚集

- 空值会自动忽略
- 布尔值有一些特殊的聚集函数：  some、 every

### 嵌套子查询

可以嵌套子查询，将子查询的结果理解成一个 关系、元组集合。

#### 集合成员资格
- in
- not in


#### 集合的比较

-  `> some`，表示至少比集合中的一个大。
-  `< some`
-  `>= some`
-  `<= some`
-  `= some`，表示至少等于集合中的一个， 等价于 `in`
-  `<> some`，表示或者大于集合中的一个，或者小于集合中的一个。 并不等价于 `not in`

- `> all`，表示比集合中所有的都大
- `< all`
- `>= all`
- `<= all`
- `= all`， 与集合所有的都相等，并不等价于 `in`
- `<> all`， 表示比集合中所有的都小，或者比集合中所有的都大，完全不在集合内，其实等价于 `not in`，


#### 空关系测试

- exists
- not exists

#### 重复元组存在性测试

- unique， 是否有重复元组， 全是唯一的，则为真
    - unique谓词在空集上计算出真值
- not unique，是否有重复元组。如果有重复的，则为真

#### from 子句中的子查询

实际上也是一个关系、元组集合。

#### with 子句

提供定义临时关系的方法，省的嵌套各种子查询。。

#### 标量子查询

查询结果为唯一的元组，或者为空值。 这种查询可以用在 select中，将一个标量作为输出。

### 数据库的修改

- 删除： `delete from r where P;`，按条件删除元组
- 插入： `insert into r values ();`
    - 也可以指定属性顺序 `insert into r(A1, A2) values(V1, V2)`
- 更新：
    - `update r set A = A * 1.05;`
    - 更新的 case 语句，按条件更新。


```
case
    when pred1 then result1
    when pred2 then result2
    ...
    when predn then resultn
    else result0
end
```
可以将case 理解成一个子句，返回一个标量（或元组）。子句里可以进行聚集运算，检查条件赋值，等等。


## 第四章 中级 SQL

### 连接表达式

- 内连接、外连接、左外连接、右外连接。
- 连接条件
    - `using`
    - `on`，更灵活，可以用谓词条件

### 视图

视图可以理解成一个命名的查询式。并没有实际保存结果，是一种 “虚”关系。

`create view v as <query expression>;`

物化视图（materialized view）： 如果有必要（比如经常访问视图），可以将视图保存下来。

视图更新： 一般并不允许对视图关系进行修改。 通过视图对底层操作会带来一系列问题。

### 事务

事务是一个查询和更新的序列，它们共同执行某项任务。

事务可以被提交或回滚。

下面SQL 语句之一会结束一个事务：
- Commit Work
- Rollback Work

当一个事务被回滚，该事物执行的所有更新所带来的影响将被撤销。

事务是原子性的，必须保持数据一致性。

默认情况下每个SQL语句自成一个事务。

### 完整性约束

完整性约束保证授权用户对数据库所做的修改不会破坏数据的一致性（一部分一致性）。 完整性约束防止对数据的意外破坏。（很多时候其实就是一种限制条件，系统会自动检查）

#### 单个关系上的约束

- 非空约束： not null
- 唯一性约束： unique
- check子句： 更详细的定义属性域

#### 参照完整性

指外码约束： 外码的值必须在被参照关系中存在。

但有时候在被参照关系中如果执行一些删除、更新操作，会破坏参照关系中的参照完整性。这时候，可以用

```
foreign key (xx) references department
    on delete cascade
    on update cascade,
```
级联删除。或者级联更新。

##### 事务中对完整性约束的违反：

事务可能包括几个步骤，在某一步之后完整性约束也许会暂时被违反，但是后面的某一步会消除这个违反。

一种方法是延时到在事务结束时检查：

`set constraints <xxx> deferred`

注意默认的方式是每个步骤都立即检查约束。有的SQL支持，有的不支持。

还有一种方法是仔细定义步骤，避免这个问题。。。

### SQL 数据类型与模式

SQL支持两种形式的用户定义数据类型：

- 独特类型（distinct type）
- 结构化数据类型

独特类型，例如：

`create type Dollars as numeric (12, 2) final;`

在赋值的时候，会做强制类型检查，这样会避免一些类型错误。但是使用时并不方便，如果类型不匹配，必须做强制类型转换。

还有一种方式是定义域：

`create domain DDollars as numerci (12, 2) not null;`

注意域和类型的区别：
1. 域上可以声明约束，例如 not null
2. 域并不是强类型的。 一个域类型的值可以被赋给另一个域类型，只要它们的基本类型是相容的。

#### create table 扩展

`create table r2 like r1;`
可以创建一个模式相同的table。

`create table r2 as <query expression>`
使用查询创建一个table， 并且注入数据（查询结果关系，元组）。

### 授权

授权：
```
  grant < 权限列表 >
  on < 关系名或视图名 >
  to < 用户/角色列表 >
```
权限有 : `selete, insert, update, delete, all`
用户： `public` 表示公开。

收回权限：
```
  revoke < 权限列表 >
  on < 关系名或视图名 >
  to < 用户/角色列表 >
```

#### 角色

对每个用户分别授权很麻烦，可以定义角色，将权限授予角色。将角色授予用户。

角色可以理解成一个组。

角色也可以授予角色， 这样会形成角色链。


#### 权限转移和权限收回

对于DBA来说，要十分注意权限管理。


## 第五章 高级 SQL

几个概念：

- 程序设计语言访问数据库、JDBC、ODBC；  
- SQL里面自定义函数和过程；  
- 触发器；
- 递归查询；
- 高级聚集特性： rank、分窗
- OLAP，数据立方体，cube、rollup

### 程序设计语言访问数据库

- JDBC： Java Database Connectivity
- ODBC： Open Database Connectivity，开放数据库互联标准。

1. 连接数据库
2. 执行 SQL 语句

是一套标准。 不同语言有不同访问数据库、执行数据库操作的方法。


### 函数和过程

SQL标准，支持声明和调用SQL函数和过程。

`create function`

也有一些语言语法。 比如 变量用 `declare` 声明； 有 `while`，`for`，`if else` 之类的语法结构。

### 触发器（trigger）

触发器是一条语句：

- 指明什么条件下执行触发器： 包括 **事件** 和 **条件**
- 指明触发器执行时的 **动作**。

在数据库中输入一个触发器，只要指定的事件发生，相应的条件满足，数据库系统就有责任去执行它。

### 递归查询

与递归函数类似。有时候，数据（元组属性）之间有层级。 比如课程和先修课关系。 如果想找到一门课的所有先修课，大概要做一个 BFS搜索。

SQL也支持一种 递归查询，来实现这种 BFS 搜索。

### 高级聚集特性

#### 1. 排名 rank

`rank() over (partition xx order by xx desc)` 类似于这种的语法，可以按某种顺序做排名（也可以分区）。

类似的还有：

- percent_rank
- cume_dist
- row_number： 给出行号
- ntile(n)： 将元组平均分到 n 个桶，给出桶号。

#### 2. 分窗

有时候需要对小窗口做聚集计算，比如计算趋势，（增大细分粒度，以及降噪）。

语法是： `avg(xxx) over (order by year rows 3 preceding)`

这句话意思就是 按照 year属性 对前三年的某个值做小窗口平均。

有其他写法，例如：

- `order by year rows unbounded preceding`， 对前面所有年份做平均
- `order by year rows between 3 preceding and 2 following`，小窗口范围是前三年和后两年， 比如 2018年： [2016, 2017, 2018, 2019]
- `order by year rows range between year - 4 and year`，包括边界，这里指 [2014, 2015, 2016, 2017, 2018]

### OLAP， 联机分析处理

好奇怪的名字。 但是比较常见，比如经常用的交叉表： 显示纵向、横向的统计特性。

交叉表（cross-tabulation），也可称为 **转轴表**（pivot-table）

还有一种是 cube： 数据立方体，对不同维度（按维属性）分组，对度量属性统计。

有些SQL支持 OLAP语法

#### CUBE

`group by cube ( a, b, c)` ： 会按照 a、b、c三个属性的所有值分组做数据立方。

比如 a 有3值、b有4种、c有3种，则得出的结果是 `(3 + 1) * (4 + 1） * （3 + 1）= 80 ` 个元组（行）。每个多出来的 1 是 `all`。


#### ROLLUP

`group by rollup (a, b, c)`

如果这样理解：使用或不使用某种属性做分组， 则 `cube(a, b, c)` 有8个分组（ $2^3$ )

而rollup只产生四个分组： `(a, b, c), (a, b), (a), ()`。共有： `3 * 4 * 3 + 3 * 4 * 1 + 3 * 1 * 1 + 1 * 1 * 1 = 52` 行。

rollup 多用在 a、b、c三个属性是层级关系的时候， 比如 省、市、县。


## 第六章 形式化关系查询语言

### 关系代数（relation algebra）

关系代数是一种 **过程化查询语言**： 它包括一个运算的集合。 这些运算以一个或两个关系为输入，产生一个新的关系作为结果。

#### 基本运算
- 选择（select）运算： 选出满足给定谓词的元组。 $\sigma_P(r)$
- 投影（project）： 属性列表。 $\Pi_{A_1,...}(r)$
- 并运算： $\cup$ 将运算结果（集合）合并。
- 集合差（set-difference）： $-$
- 笛卡尔积： $\times$
- 更名运算： 实际上是将一个关系代数表达式命名： $\rho_x(E)$ 指将 关系表达式 $E$ 更名为 $x$

#### 附加的关系代数运算

- 集合交  $\cap$
- 自然连接（）： 自然连接运算首先形成它的两个参数（关系）的笛卡尔积，然后基于两个关系模式中都出现的属性上的相等性进行选择，最后还要去除重复属性。
- 赋值运算： 临时关系变量，可以简化表达式
- 外连接运算： 左外连接、右外连接、全外连接

#### 扩展的关系代数运算

- 广义投影：  $\Pi_{F_1, F_2, \cdots, F_n}(E)$ ，每个 $F$ 都是 对 $E$ 模式的属性的算术表达式。
- 聚集运算： $_ {G_1,G_2,\cdots,G_n}\mathcal{G}_{F_1(A_1), F_2(A_2), \cdots, F_m(A_m)}(E)$，前面 $G$ 表示用于分组的属性。

### 元组关系演算

上述关系代数表达式提供产生查询结果的过程序列。

元组关系演算是非过程化的（nonprocedural）查询语言。 它只描述所需信息，而不给出获得该信息的具体过程。

### 域关系演算

关系演算的另一种形式，使用从属性域中取值的域变量，而不是整个元组的值。

关系代数是SQL语言的基础，域关系演算是被广泛采用的QBE语言的理论基础。


### 小结

关系代数表达式与SQL联系紧密，关系代数是SQL的理论基础。

关系演算是简洁的、形式化的语言，并不适合那些偶尔使用数据库系统的用户。
