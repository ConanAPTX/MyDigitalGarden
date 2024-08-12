---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/分区表_MySql 模式/","dgPassFrontmatter":true}
---



### 分区表_MySql 模式


#### 1 分区分类

 MySQL模式目前支持的分区类型如下：*Range 分区*，*Range Columns 分区*，*List 分区*，*List Columns 分区*， *Hash 分区*，*Key 分区*，*组合分区*；


##### 1.1 Range 分区

*Range 分区* 根据分区表定义时为每个分区建立的分区键值范围，将数据映射到相应的分区中。它是常见的分区类型，经常跟日期类型一起使用。例如：可以将业务日志表按日/周/月分区。

*Range 分区* 的分区键必须是【`整数类型`】或 【`YEAR 类型`】，如果对其他类型的日期字段分区，则需要使用函数进行转换。Range 分区的分区键仅支持一列；

*RANGE 分区* 是按用户指定的表达式范围将每一条记录划分到不同分区；常用场景： 按时间字段进行分区；

目前提供对 range 分区的分区操作功能，能 add / drop 分区；
1. 存在maxvalue的分区的情况，由于add分区现在只能加在最后，所以会添加分区失败；
2. 不存在maxvalue的分区的情况，当插入的数据超出当前分区的最大值，则会插入失败；

```sql
-- 使用示例
CREATE TABLE `info_t`(id INT, gmt_create TIMESTAMP, info VARCHAR(20), PRIMARY KEY (gmt_create))
	PARTITION BY RANGE(UNIX_TIMESTAMP(gmt_create))(
		PARTITION p0 VALUES LESS THAN (UNIX_TIMESTAMP('2015-01-01 00:00:00')),
		PARTITION p1 VALUES LESS THAN (UNIX_TIMESTAMP('2016-01-01 00:00:00')),
		PARTITION p2 VALUES LESS THAN (UNIX_TIMESTAMP('2017-01-01 00:00:00'))
	);
```


##### 1.2 Range Columns 分区

*Range Columns 分区*与 *Range 分区* 的作用基本类似，不同之处在于：
1. Range Columns 分区的分区键的结果不要求是整型，可以是以下数据类型：
	1. 支持所有整数类型，包括 TINYINT、 SMALLINT、 MEDIUMINT、 INT ( INTEGER) 和 BIGINT。
		1. 不支持将其他数值数据类型（例如，DECIMAL 或 FLOAT）的列作为分区键。
	2. 支持时间类型 DATE 和 DATETIME。
		1. 不支持使用其他与日期或时间相关数据类型的列作为分区键。
	3. 支持字符串类型 CHAR、 VARCHAR、 BINARY 和 VARBINARY。
		1. 不支持将 TEXT 或 BLOB 数据类型的列作为分区键。    
2. Range Columns 分区的分区键不能使用表达式；
3. Range Columns 分区的分区键可以写多个列（即列向量）；

*RANGE COLUMNS 分区* 与 *RANGE 分区* 类似，但不同点在于 RANGE COLUMNS 分区可以按一个或多个分区键向量进行分区，并且每个分区键的类型除了 INT 类型还可以支持其他类型，比如 VARCHAR、DATETIME 等；


```sql
-- 使用示例
CREATE TABLE `info_t`(id INT, gmt_create DATETIME, info VARCHAR(20), PRIMARY KEY (gmt_create))
	PARTITION BY RANGE COLUMNS(gmt_create)(
		PARTITION p0 VALUES LESS THAN ('2015-01-01 00:00:00’),
		PARTITION p1 VALUES LESS THAN ('2016-01-01 00:00:00’),
		PARTITION p2 VALUES LESS THAN ('2017-01-01 00:00:00’),
		PARTITION p3 VALUES LESS THAN (MAXVALUE)
	);
```

##### 1.3 List 分区

*List 分区* 使得您可以显式的控制记录行如何映射到分区，具体方法是为每个分区的分区键指定一组离散值列表，这点跟 Range 分区和 Hash 分区都不同。List 分区的优点是可以方便的对无序或无关的数据集进行分区。

*List 分区* 的分区键可以是列名，也可以是一个表达式，分区键必须是整数类型或 YEAR 类型；

LIST 分区是根据枚举类型的值来划分分区的，主要用于枚举类型；

1. LIST分区的限制和要求
	1. 分区表达式的结果必须是int类型；
	2. 不能写向量，例如partition by list(c1, c2)
	
```sql
create table t1 (c1 int, c2 int) partition by list(c1)(
	partition p0 values in (1,2,3), partition p1 values in (5, 6)，partition p2 values in (default)
);
```


2. List columns 和 list 的区别是：
	1. list columns 分区不要求是int类型，可以是任意类型；
	2. list columns 分区不能写表达式；
	3. list columns 分区支持向量；



##### 1.4 List Columns 分区

*List Columns 分区*与 *List 分区*的作用基本相同，不同之处在于：

1. List Columns 分区的分区键不要求是整型，可以是以下数据类型：
	1. 支持所有整数类型，包括 TINYINT、 SMALLINT、 MEDIUMINT、 INT ( INTEGER) 和 BIGINT。
		1. 不支持将其他数值数据类型（例如，DECIMAL 或 FLOAT）的列作为分区键。
	2. 支持时间类型 DATE 和 DATETIME。
		1. 不支持使用其他与日期或时间相关数据类型的列作为分区键。
	3. 支持字符串类型 CHAR、 VARCHAR、 BINARY 和 VARBINARY；
		1. 不支持将 TEXT 或 BLOB 数据类型的列作为分区键；
2. List Columns 分区的分区键不能使用表达式；
3. List Columns 分区支持多个分区键，List 分区仅支持单分区键。


##### 1.5 Hash 分区
*Hash 分区*适合于对不能用 *Range 分区*、*List 分区*方法的场景，它的实现方法简单，通过对分区键上的 Hash 函数值来散列记录到不同分区中。如果您的数据符合下列特点，使用 Hash 分区是个很好的选择：

1. 不能指定数据的分区键的列表特征;
2. 不同范围内的数据大小相差非常大，并且很难手动调整均衡。
3. 使用 Range 分区后数据聚集严重。
4. 并行 DML、分区剪枝和分区连接等性能非常重要。


Hash 分区的分区键必须是整数类型或 YEAR 类型，并且可以使用表达式。

*HASH 分区* 需要指定分区键和分区个数，适合于对不能用 Range 分区、List 分区方法的场景。通过对分区键上的 Hash 函数值来散列记录到不同分区中；通常用于给定分区键的点查询，例如按照用户id来分区。 HASH 分区通常能消除热点查询；

```sql
create table t1 (c1 int, c2 int) partition by hash(c1 + 1) partitions 5
	/*
	其中 partition by hash(c1+1) 指定了分区键c1和分区表达式c1 + 1；
	partitions 5 指定了分区数;
	*/
```

> [!NOTE] OB MySQL 模式的 Hash 分区限制和要求：
> 1. 分区表达式的结果必须是 int 类型；
> 2. 不能写向量，例如 partition by hash(c1, c2)

##### 1.6 Key 分区

Key 分区与 Hash 分区类似，也是通过对分区个数取模的方式来确定数据属于哪个分区。

Key 分区有如下特点：

1. Key 分区的分区键不要求为整型，可以为除 TEXT 和 BLOB 以外的其他数据类型。
2. Key 分区的分区键不能使用表达式。
3. Key 分区的分区键支持向量。
4. Key 分区的分区键中不指定任何列时，表示 Key 分区的分区键是主键。
    
    示例如下：
```sql
obclient> CREATE TABLE tbl1 (col1 INT PRIMARY KEY, col2 INT)  PARTITION BY KEY() PARTITIONS 5;
Query OK, 0 rows affected
```


*KEY 分区*与 *HASH 分区* 类似，也是通过对分区个数取模的方式来确定数据属于哪个分区，不同在于：
1. 系统会对 Key 分区键做一个内部默认的 Hash 函数后再取模；
2. 用户通常没有办法自己通过简单的计算来得知某一行属于哪个分区
3. 测试发现 KEY 分区所用到的 HASH 函数不太均匀；
```sql
create table t1 (c1 varchar(16), c2 int) partition by key(c1) partitions 5
```
4. KEY 分区不要求是 int 类型，可以是任意类型；
5. KEY 分区不能写表达式（与 HASH 分区区别）
6. KEY 分区支持向量
7. KEY 分区有一个特殊的语法；
```sql
create table t1 (c1 int primary key, c2 int) partition by key() partitions 5
	-- KEY 分区分区键不写任何 column，表示 key 分区的列是主键；
```


##### 1.7 组合分区
组合分区通常是先使用一种分区策略，然后在子分区再使用另外一种分区策略，适合于业务表的数据量非常大时。使用组合分区能发挥多种分区策略的优点。

> [!NOTE] 分区注意事项：
> 1. 在 MySQL 模式中，使用自增列作为分区键时，应注意以下事项：
> 	1. 在 OceanBase 数据库中，自增列的值全局唯一，但在分区内不保证递增；
> 	2. 与其他分区方式相比，对使用自增列作为分区键的分区表的插入操作由于无法有效路由，会产生跨机事务，性能会有一定的下降


##### 1.8 生成列分区
一级分区

*生成列* 是指这一列是由其他列计算而得；*生成列分区* 是指将生成列作为分区键进行分区，该功能能够更好的满足期望将某些字段进行一定处理后作为分区键的需求（比如提取一个字段的一部分，作为分区键）；

```sql
-- 使用示例
CREATE TABLE gc_part_t(
	t_key varchar(10) PRIMARY KEY, 
	gc_user_id VARCHAR(4) GENERATED ALWAYS AS (SUBSTRING(t_key, 1, 4)) VIRTUAL, 
	c3 INT
) PARTITION BY KEY(gc_user_id) PARTITIONS 10;
```

#### 2 分区名字规则
对于 List 和 Range 分区，因为在创建表的过程中就指定了分区的名字，所以名字就是当时指定的名字。

对于 Hash/Key 分区，创建时如果没有指定分区的名字，分区的命名由系统根据命名规则完成。具体表现为：
-   当 Hash/Key 分区为一级分区时，则每个分区分别命名为 p0、p1、...、pn。
-   当 Hash/Key 分区为二级分区时，则每个分区分别命名为 sp0、sp1、 ...、spn。

对于模板化二级分区表，定义二级分区后，系统根据二级分区的命名规则完成，其二级分区的命名规则为 `($part_name)s($subpart_name)`。对于非模板化二级分区表，二级分区的分区名即为⾃定义的分区名


#### 3 分区表达式允许使用的函数
对于 *Rnage* 、*List* 以及 *Hash 分区*，其分区键允许使用表达式；
在 OceanBase 数据库的 MySQL 模式中，仅允许使用以下函数作为分区表达式：[ABS()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222591)，[CEILING()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222580)， [DAY()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222626)， [DAYOFMONTH()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222630)， [DAYOFWEEK()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222679)，[DAYOFYEAR()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222661)，[DATEDIFF()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222640)， [EXTRACT()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222637)， [FLOOR()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222601)， [FROM_DAYS()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222653)，[HOUR()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222656)，[MICROSECOND()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222650)，[MINUTE()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222636)，[MOD()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222611)，[MONTH()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222649)，[QUARTER()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222662)， [SECOND()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222655)，[TIME_TO_SEC()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222654)，[TO_DAYS()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222666)，[TO_SECONDS()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222621)， [TIME_TO_USEC()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222665)，[UNIX_TIMESATMP()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222670)，[WEEKDAY()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222651)，[WEEKOFYEAR()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222678)，[YEAR()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222673)，[YEARWEEK()](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222631)，；



#### 4 分区管理

> [!NOTE] 使用限制：
> 1. 只有 range 分区，可以删除任意一个一级range分区；
> 2. 只能以 append 方式往后添加分区，也就是说，新加分区的 range value 总是最大的；


```sql
-- 1.增加分区
	-- 伴随数据量增长，range分区需要能够扩展 -> ADD PARTITION；
	-- 语法: ALTER TABLE ... ADD PARTITION
	
	ALTER TABLE members ADD PARTITION(PARTITION p3 VALUES LESS THAN(2000));
```


```sql
-- 2.删除分区
	-- 对于按时间范围分区的表，有时需要作过期数据清理 -> DROP PARTITION；
	-- 语法: ALTER TABLE ... DROP PARTITION

	ALTER TABLE members DROP PARTITION(P3);
```



### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 参考文档
1. `MySql 模式`
	1. *V4.2.1：参考指南 > 系统原理 > 数据库对象 > MySql 模式 > 分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221003)，；
	2. *分区概述*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220228)，；
	3. *创建分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220224)，；
	4. *分区裁剪*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220226)，；
	5. *指定分区查询数据*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220231)，；
2. `Oracle 模式`
	1. *V4.2.1：参考指南 > 系统原理 > 数据库对象 > Oracle 模式 > 分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221034)，；
	2. *分区概述*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220162)，；
	3. *创建分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220163)，；
	4. *修改分区规则*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220169)，；
	5. *重命名分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220167)，；
	6. *添加分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220161)，；
	7. *删除分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220166)，；
	8. *Truncate 分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220170)，；
	9. *分区裁剪*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220168)，；
	10. *指定分区查询数据*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220164)，；




</div></div>



### 删除备份
#### 1 二级分区

*二级分区* 是指按照两个维度来把数据拆分成分区。最常用的地方就是类似用户账单领域，会按照 user_id 做 HASH 分区，按照账单创建时间做 RANGE 分区；

n 示例：按两个维度划分数据


```sql
-- 使用示例
CREATE TABLE history_t (
	user_id INT,
	gmt_create DATETIME, info VARCHAR(20),
	PRIMARY KEY(user_id, gmt_create)
)
PARTITION BY RANGE COLUMNS (gmt_create)
SUBPARTITION BY HASH(user_id) SUBPARTITIONS 3(
	PARTITION p0 VALUES LESS THAN ('2014-11-11'),
	PARTITION p1 VALUES LESS THAN ('2015-11-11'),
	PARTITION p2 VALUES LESS THAN ('2016-11-11'),
	PARTITION p3 VALUES LESS THAN ('2017-11-11')
);
```

##### 1.1 二级分区支持的分区方式

1. 二级分区支持的分区方式
2. HASH/KEY + RANGE/RANGE_COLUMNS 分区
3. RANGE/RANGE_COLUMNS + HASH/KEY 分区
4. LIST/LIST_COLUMNS+ RANGE/RANGE_COLUMNS 分区
5. RANGE/RANGE_COLUMNS + LIST/LIST_COLUMNS分区
6. HASH/KEY + LIST/LIST_COLUMNS 分区
7. LIST/LIST_COLUMNS+HASH/KEY 分区


对于 RANGE 分区的分区操作 add/drop，必须是 RANGE 分区做为一级分区的方式。所以强烈建议用 RANGE + HASH 的分区方式，而不是 HASH + RANGE；

