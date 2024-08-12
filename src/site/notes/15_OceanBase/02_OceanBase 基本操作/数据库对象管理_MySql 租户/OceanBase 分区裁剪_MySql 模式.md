---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 分区裁剪_MySql 模式/","dgPassFrontmatter":true}
---


### OceanBase 分区裁剪_MySql 模式
通过分区裁剪功能（Partition Pruning）可以避免访问无关分区，使 SQL 的执行效率得到大幅度提升。本文主要介绍了分区裁剪的原理和应用。

当用户访问分区表时，往往只需要访问其中的部分分区，通过优化器避免访问无关分区的优化过程我们称之为`分区裁剪`（Partition Pruning）。分区裁剪是分区表提供的重要优化手段，通过分区裁剪，SQL 的执行效率可以得到大幅度提升。您可以利用分区裁剪的特性，在访问中加入定位分区的条件，避免访问无关数据，优化查询性能。

分区裁剪本身是一个比较复杂的过程，优化器需要根据用户表的分区信息和 SQL 中指定的条件，抽取出相关的分区信息。由于 SQL 中的条件往往比较复杂，整个抽取逻辑的复杂性也随之增加，这一过程由 OceanBase 数据库中的 Query Range 子模块完成；

#### 1 一级分区裁剪的基本原理
##### 1.1 Hash/List 分区
分区裁剪就是根据 `where` 子句里面的条件并且计算得到分区列的值，然后通过结果判断需要访问哪些分区。如果分区条件为表达式，且该表达式作为一个整体出现在等值条件里，也可以做分区裁剪；

分区条件为表达式 `c1 + c2`，且该表达式作为一个整体出现在等值条件里的分区裁剪的示例如下：
```sql
obclient> CREATE TABLE t1(c1 INT,c2 INT) PARTITION BY HASH(c1 + c2) PARTITIONS 5;

obclient> EXPLAIN SELECT * FROM t1 WHERE c1 + c2 = 1 \G
*************************** 1. row ***************************
Query Plan: 
===================================
|ID|OPERATOR  |NAME|EST. ROWS|COST|
-----------------------------------
|0 |TABLE SCAN|t1  |5        |1303|
===================================

Outputs & filters:
-------------------------------------
  0 - output([t1.c1], [t1.c2]), filter([t1.c1 + t1.c2 = 1]),
      access([t1.c1], [t1.c2]), partitions(p1)
```


##### 1.2 Range 分区
您可以通过 `where` 子句的分区键的范围跟表定义的分区范围的交集来确定需要访问的分区。

对于 Range 分区：
1. 当分区条件为列时，无论查询条件为等值条件还是非等值条件，均支持分区裁剪；
2. 当分区条件为表达式，且查询条件为等值条件时，支持分区裁剪；

例如，分区条件为表达式 `c1`，查询条件为非等值条件 `c1 < 150 and c1 > 100`，可以进行分区裁剪。示例如下：
```sql
obclient> CREATE TABLE t1(c1 INT,c2 INT) PARTITION BY RANGE(c1)
       (PARTITION p0 VALUES LESS THAN(100),
        PARTITION p1 VALUES LESS THAN(200)
        );
Query OK, 0 rows affected

obclient> EXPLAIN SELECT * FROM t1 WHERE c1 < 150 and c1 > 110\G
*************************** 1. row ***************************
Query Plan: 
===================================
|ID|OPERATOR  |NAME|EST. ROWS|COST|
-----------------------------------
|0 |TABLE SCAN|t1  |1        |46  |
===================================

Outputs & filters:
-------------------------------------
  0 - output([t1.c1], [t1.c2]), filter([t1.c1 < 150], [t1.c1 > 110]),
      access([t1.c1], [t1.c2]), partitions(p1)

1 row in set
```

此外，如果分区条件的表达式使用了 `YEAR()`、`TO_DAYS()` 或 `TO_SECONDS()` 等时间类型的函数，也支持根据查询条件所指定的范围进行分区裁剪。

示例如下：

```sql
obclient> CREATE TABLE tbl_r (log_id BIGINT NOT NULL,log_value VARCHAR(50),log_date datetime NOT NULL)
         PARTITION BY RANGE(to_days(log_date)) 
          (PARTITION M202001 VALUES LESS THAN(to_days('2020/02/01'))
         , PARTITION M202002 VALUES LESS THAN(to_days('2020/03/01'))
         , PARTITION M202003 VALUES LESS THAN(to_days('2020/04/01'))
         , PARTITION M202004 VALUES LESS THAN(to_days('2020/05/01'))
         , PARTITION M202005 VALUES LESS THAN(to_days('2020/06/01'))
         , PARTITION M202006 VALUES LESS THAN(to_days('2020/07/01'))
         , PARTITION M202007 VALUES LESS THAN(to_days('2020/08/01'))
         , PARTITION M202008 VALUES LESS THAN(to_days('2020/09/01'))
         , PARTITION M202009 VALUES LESS THAN(to_days('2020/10/01'))
         , PARTITION M202010 VALUES LESS THAN(to_days('2020/11/01'))
         , PARTITION M202011 VALUES LESS THAN(to_days('2020/12/01'))
         , PARTITION M202012 VALUES LESS THAN(to_days('2021/01/01'))
          );
Query OK, 0 rows affected

obclient> EXPLAIN SELECT * FROM tbl_r WHERE log_date > '2020/07/15' and log_date <'2020/10/07'\G
*************************** 1. row ***************************
Query Plan: 
====================================================
|ID|OPERATOR               |NAME    |EST. ROWS|COST|
----------------------------------------------------
|0 |PX COORDINATOR         |        |1        |183 |
|1 | EXCHANGE OUT DISTR    |:EX10000|1        |183 |
|2 |  PX PARTITION ITERATOR|        |1        |183 |
|3 |   TABLE SCAN          |tbl_r   |1        |183 |
====================================================

Outputs & filters:
-------------------------------------
  0 - output([INTERNAL_FUNCTION(tbl_r.log_id, tbl_r.log_value, tbl_r.log_date)]), filter(nil)
  1 - output([INTERNAL_FUNCTION(tbl_r.log_id, tbl_r.log_value, tbl_r.log_date)]), filter(nil), dop=1
  2 - output([tbl_r.log_date], [tbl_r.log_id], [tbl_r.log_value]), filter(nil)
  3 - output([tbl_r.log_date], [tbl_r.log_id], [tbl_r.log_value]), filter([tbl_r.log_date > ?], [tbl_r.log_date < ?]),
      access([tbl_r.log_date], [tbl_r.log_id], [tbl_r.log_value]), partitions(p[6-9])

1 row in set
```

#### 2 二级分区裁剪的基本原理
对于二级分区，先按照一级分区键确定一级分区需要访问的分区，再通过二级分区键确定二级分区需要访问的分区。最后做一个乘积确定二级分区需要访问的所有物理分区。

以下示例中，经过计算得到一级分区裁剪结果是 p0，而二级分区裁剪的结果是 sp0，所以访问的物理分区为 p0sp0；
```sql
obclient> CREATE TABLE tbl2_rr(col1 INT,col2 INT)
       PARTITION BY RANGE(col1)
       SUBPARTITION BY RANGE(col2)
       SUBPARTITION TEMPLATE
       (SUBPARTITION sp0 VALUES LESS THAN(1000),
        SUBPARTITION sp1 VALUES LESS THAN(2000)
        ) 
       (PARTITION p0 VALUES LESS THAN(100),
        PARTITION p1 VALUES LESS THAN(200)
       ) ; 
Query OK, 0 rows affected

obclient> EXPLAIN SELECT * FROM tbl2_rr
       WHERE (col1 = 1 or col1 = 2) and (col2 > 101 and col2 < 150) \G
*************************** 1. row ***************************
Query Plan: ======================================
|ID|OPERATOR  |NAME   |EST. ROWS|COST|
--------------------------------------
|0 |TABLE SCAN|TBL2_RR|99       |53  |
======================================

Outputs & filters:
-------------------------------------
  0 - output([TBL2_RR.COL1], [TBL2_RR.COL2]), filter(nil),
      access([TBL2_RR.COL1], [TBL2_RR.COL2]), partitions(p0sp0)

1 row in set
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



