---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/EXPLAIN，查看执行计划/","dgPassFrontmatter":true}
---


### EXPLAIN，查看执行计划
执行计划是对一条 SQL 查询语句在数据库中执行过程的描述，通常用于分析某条 SQL 的性能问题，读懂执行计划是 SQL 优化的先决条件。用户可以通过 EXPLAIN 命令查看优化器针对指定 SQL 生成的逻辑执行计划；

#### 1 EXPLAIN 命令格式
`EXPLAIN` 与 `DESCRIBE`、`DESC` 互为同义词；OceanBase 数据库的执行计划命令有三种模式：EXPLAIN BASIC、EXPLAIN 和 EXPLAIN EXTENDED；

```sql
/* 语法：
	EXPLAIN [explain_type] dml_statement;
	  explain_type：BASIC|OUTLINE|EXTENDED|EXTENDED_NOADDR|PARTITIONS|FORMAT={TRADITIONAL|JSON}  
	  dml_statement: SELECT | DELETE | INSERT | REPLACE| UPDATE
参数：
	BASIC：指定输出计划的基础信息，如算子 ID、算子名称、所引用的表名；
	OUTLINE：指定输出的计划信息包含 OUTLINE 信息；
	EXTENDED：EXPLAIN 产生附加信息，包括：每个算子的输入列和输出列，访问表的分区信息，当前使用的 Filter 信息，
		如果当前算子使用了索引，显示所使用的索引列及抽取的 Query Range；
	EXTENDED_NOADDR：以简约的方式展示附加信息；
	PARTITIONS：显示分区相关信息；
	FORMAT={TRADITIONAL|JSON}：指定 EXPALIN 的输出格式：
		TRADITIONAL：表示以表格格式显示输出，这也是默认设置；
		JSON：KEY:VALUE 输出格式，JSON 显示为JSON 字符串，包括 EXTENDED 和 PARTITIONS 信息；  
	PRETTY | PRETTY_COLOR：对于复杂的执行计划，可以使用该项将计划树中的父节点和子节点使用树线或彩色树线连接起来，使得执行计划展示更方便阅读；
*/
```

#### 2 EXPLAIN 命令使用示例
创建测试环境可以参考：[[15_OceanBase/05_OceanBase 性能调优/创建测试环境\|创建测试环境]]，；

```sql
-- 展示执行计划
EXPLAIN BASIC SELECT * FROM t1,t2 WHERE t1.a = t2.a\G;
EXPLAIN SELECT * FROM t1,t2 WHERE t1.a = t2.a\G;
EXPLAIN EXTENDED SELECT * FROM t1,t2 WHERE t1.a = t2.a\G;
EXPLAIN EXTENDED PRETTY_COLOR SELECT  * FROM p1table p1 JOIN p2table p2 ON p1.c1=p2.c2\G;
/* 输出结果：
	*************************** 1. row ***************************
	Query Plan: ==========================================================
	|ID|OPERATOR                     |NAME    |EST. ROWS|COST|
	----------------------------------------------------------
	|0 |PX COORDINATOR               |        |1        |278 |
	|1 | EXCHANGE OUT DISTR          |:EX10001|1        |277 |
	|2 |  HASH JOIN                  |        |1        |276 |
	|3 |  ├PX PARTITION ITERATOR     |        |1        |92  |
	|4 |  │ TABLE SCAN               |P1      |1        |92  |
	|5 |  └EXCHANGE IN DISTR         |        |1        |184 |
	|6 |    EXCHANGE OUT DISTR (PKEY)|:EX10000|1        |184 |
	|7 |     PX PARTITION ITERATOR   |        |1        |183 |
	|8 |      TABLE SCAN             |P2      |1        |183 |
	==========================================================
	
	Outputs & filters:
	-------------------------------------
	  0 - output([INTERNAL_FUNCTION(P1.C1, P1.C2, P2.C1, P2.C2)]), filter(nil)
	  1 - output([INTERNAL_FUNCTION(P1.C1, P1.C2, P2.C1, P2.C2)]), filter(nil), dop=1
	  2 - output([P1.C1], [P2.C2], [P1.C2], [P2.C1]), filter(nil),
	      equal_conds([P1.C1 = P2.C2]), other_conds(nil)
	  3 - output([P1.C1], [P1.C2]), filter(nil)
	  4 - output([P1.C1], [P1.C2]), filter(nil),
	      access([P1.C1], [P1.C2]), partitions(p[0-1])
	  5 - output([P2.C2], [P2.C1]), filter(nil)
	  6 - (#keys=1, [P2.C2]), output([P2.C2], [P2.C1]), filter(nil), dop=1
	  7 - output([P2.C1], [P2.C2]), filter(nil)
	  8 - output([P2.C1], [P2.C2]), filter(nil),
	      access([P2.C1], [P2.C2]), partitions(p[0-3])
	1 row in set 
	
OceanBase 数据库执行计划中的各列的含义如下：
	ID：执行树按照前序遍历的方式得到的编号(从 0 开始)；
	OPERATOR：操作算子的名称；
	NAME：对应表操作的表名(索引名)；
	EST. ROWS：估算该操作算子的输出行数；
	COST：该操作算子的执行代价(微秒)；
*/
```


#### 3 执行计划算子
1. [TABLE SCAN 算子 V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357886)，[TABLE SCAN 算子 V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-10000000001701053)，；
2. [SORT_V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702241)，；



### 参考文档：
1. [SQL 执行计划简介_V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001700649)，；