---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 分区裁剪_Oracle 模式/","dgPassFrontmatter":true}
---


### 分区裁剪

通过分区裁剪功能（Partition Pruning）可以避免访问无关分区，使 SQL 的执行效率得到大幅度提升。本文主要介绍了分区裁剪的原理和应用；

当用户访问分区表时，往往只需要访问其中的部分分区，通过优化器避免访问无关分区的优化过程我们称之为*分区裁剪*（Partition Pruning）。分区裁剪是分区表提供的重要优化手段，通过分区裁剪，SQL 的执行效率可以得到大幅度提升。您可以利用分区裁剪的特性，在访问中加入定位分区的条件，避免访问无关数据，优化查询性能；

分区裁剪本身是一个比较复杂的过程，优化器需要根据用户表的分区信息和 SQL 中给定的条件，抽取出相关的分区信息。由于 SQL 中的条件往往比较复杂，整个抽取逻辑的复杂性也随之增加，这一过程由 OceanBase 数据库中的 Query Range 子模块完成；


#### 1 一级分区裁剪的基本原理
##### 1.1 Hash/List 分区
分区裁剪就是根据 `where` 子句里面的条件并且计算得到分区列的值，然后通过结果判断需要访问哪些分区。查询条件为等值条件时，才可以进行分区裁剪；

【未验证，有可能 MySql 模式支持】如果分区函数为表达式，且该表达式作为一个整体出现在等值条件里，也可以做分区裁剪；

创建一个分区表，分区键为 `c1`，查询条件为一个等值条件，示例如下：

```sql
-- 创建分区表
CREATE TABLE t1(c1 INT,c2 INT) PARTITION BY HASH(c1) PARTITIONS 5;
-- CREATE TABLE t1 (c1 INT,c2 INT) PARTITION BY HASH(c1 + c2)partitions 5;  -- 【未验证，有可能 MySql 模式支持】；

-- 分区选择
explain select * from t1 partition(p4);

-- 通过 EXPLAIN 查看分区裁剪的结果
EXPLAIN SELECT * FROM t1 WHERE c1 = 1 or c1 = 4;
EXPLAIN SELECT * FROM t1 WHERE c1 = 1;
+------------------------------------------------------------------------------------+
| Query Plan                                                                         |
+------------------------------------------------------------------------------------+
| ===============================================                                    |
| |ID|OPERATOR       |NAME|EST.ROWS|EST.TIME(us)|                                    |
| -----------------------------------------------                                    |
| |0 |TABLE FULL SCAN|T1  |1       |4           |                                    |
| ===============================================                                    |
| Outputs & filters:                                                                 |
| -------------------------------------                                              |
|   0 - output([T1.C1], [T1.C2]), filter([T1.C1 = 1]), rowset=16                     |
|       access([T1.C1], [T1.C2]), partitions(p1)                                     |
|       is_index_back=false, is_global_index=false, filter_before_indexback[false],  |
|       range_key([T1.__pk_increment]), range(MIN ; MAX)always true                  |
+------------------------------------------------------------------------------------+
11 rows in set
```

如果查询条件中使用了非等值条件，则无法进行分区裁剪；

```sql
-- 如果查询条件中使用了非等值条件，则无法进行分区裁剪；
EXPLAIN SELECT * FROM t1 WHERE c1 > 1;
+------------------------------------------------------------------------------------+
| Query Plan                                                                         |
+------------------------------------------------------------------------------------+
| =============================================================                      |
| |ID|OPERATOR                 |NAME    |EST.ROWS|EST.TIME(us)|                      |
| -------------------------------------------------------------                      |
| |0 |PX COORDINATOR           |        |1       |21          |                      |
| |1 |└─EXCHANGE OUT DISTR     |:EX10000|1       |21          |                      |
| |2 |  └─PX PARTITION ITERATOR|        |1       |19          |                      |
| |3 |    └─TABLE FULL SCAN    |T1      |1       |19          |                      |
| =============================================================                      |
| Outputs & filters:                                                                 |
| -------------------------------------                                              |
|   0 - output([INTERNAL_FUNCTION(T1.C1, T1.C2)]), filter(nil), rowset=16            |
|   1 - output([INTERNAL_FUNCTION(T1.C1, T1.C2)]), filter(nil), rowset=16            |
|       dop=1                                                                        |
|   2 - output([T1.C1], [T1.C2]), filter(nil), rowset=16                             |
|       force partition granule                                                      |
|   3 - output([T1.C1], [T1.C2]), filter([T1.C1 > 1]), rowset=16                     |
|       access([T1.C1], [T1.C2]), partitions(p[0-4])                                 |
|       is_index_back=false, is_global_index=false, filter_before_indexback[false],  |
|       range_key([T1.__pk_increment]), range(MIN ; MAX)always true                  |
+------------------------------------------------------------------------------------+
19 rows in set
```


##### 1.2 Range 分区
在 V2.x，3.x 版本中：对于Range 分区，因为考虑到函数单调性，如果分区表达式是一个函数并且查询条件是一个范围，则不支持分区裁剪；【`未实际验证，OBCP 培训文档上写的`】

通过 `where` 子句的分区键和表定义的分区范围的交集来确定需要访问的分区；无论查询条件为*等值条件* 还是*非等值条件*，均可以进行分区裁剪；

例如，以下分区表的分区键为 `c1`，当查询条件为非等值条件 `c1 < 150 and c1 > 100`时，可以进行分区裁剪，示例如下

```sql
-- 创建分区表
CREATE TABLE t1(c1 INT,c2 INT) PARTITION BY RANGE(c1)(
	PARTITION p0 VALUES LESS THAN(100),
    PARTITION p1 VALUES LESS THAN(200)
);

-- 通过 EXPLAIN 查看分区裁剪的结果
EXPLAIN SELECT * FROM t1 WHERE c1 < 150 and c1 > 110;
+------------------------------------------------------------------------------------------+
| Query Plan                                                                               |
+------------------------------------------------------------------------------------------+
| ===============================================                                          |
| |ID|OPERATOR       |NAME|EST.ROWS|EST.TIME(us)|                                          |
| -----------------------------------------------                                          |
| |0 |TABLE FULL SCAN|T1  |1       |4           |                                          |
| ===============================================                                          |
| Outputs & filters:                                                                       |
| -------------------------------------                                                    |
|   0 - output([T1.C1], [T1.C2]), filter([T1.C1 < 150], [T1.C1 > 110]), rowset=16          |
|       access([T1.C1], [T1.C2]), partitions(p1)                                           |
|       is_index_back=false, is_global_index=false, filter_before_indexback[false,false],  |
|       range_key([T1.__pk_increment]), range(MIN ; MAX)always true                        |
+------------------------------------------------------------------------------------------+
11 rows in set

```


#### 2 二级分区裁剪的基本原理
对于二级分区，先按照一级分区键确定一级分区需要访问的分区，再通过二级分区键确定二级分区需要访问的分区。最后做一个乘积确定二级分区需要访问的所有物理分区；

以下示例中，经过计算得到一级分区裁剪结果是 `p0`，而二级分区裁剪的结果是 `sp0`，所以访问的物理分区为 `p0sp0`；


```sql
CREATE TABLE tbl2_rr(col1 INT,col2 INT)
	PARTITION BY RANGE(col1)
    SUBPARTITION BY RANGE(col2)
    SUBPARTITION TEMPLATE(
	    SUBPARTITION sp0 VALUES LESS THAN(1000),SUBPARTITION sp1 VALUES LESS THAN(2000)
    ) (PARTITION p0 VALUES LESS THAN(100),PARTITION p1 VALUES LESS THAN(200));

-- 通过 EXPLAIN 查看分区裁剪的结果
EXPLAIN SELECT * FROM tbl2_rr WHERE (col1 = 1 or col1 = 2) and (col2 > 101 and col2 < 150);
+-----------------------------------------------------------------------------------------------------------------------------------------------------+
| Query Plan                                                                                                                                          |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+
| ==================================================                                                                                                  |
| |ID|OPERATOR       |NAME   |EST.ROWS|EST.TIME(us)|                                                                                                  |
| --------------------------------------------------                                                                                                  |
| |0 |TABLE FULL SCAN|TBL2_RR|1       |4           |                                                                                                  |
| ==================================================                                                                                                  |
| Outputs & filters:                                                                                                                                  |
| -------------------------------------                                                                                                               |
|   0 - output([TBL2_RR.COL1], [TBL2_RR.COL2]), filter([TBL2_RR.COL2 > 101], [TBL2_RR.COL2 < 150], [TBL2_RR.COL1 = 1 OR TBL2_RR.COL1 = 2]), rowset=16 |
|       access([TBL2_RR.COL1], [TBL2_RR.COL2]), partitions(p0sp0)                                                                                     |
|       is_index_back=false, is_global_index=false, filter_before_indexback[false,false,false],                                                       |
|       range_key([TBL2_RR.__pk_increment]), range(MIN ; MAX)always true                                                                              |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+
11 rows in set
```
某些场景下，分区裁剪可能会存在一定程度的放大，但优化器可以确保裁剪的结果是所需访问数据的超集，不会存在丢失数据的情况；


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



