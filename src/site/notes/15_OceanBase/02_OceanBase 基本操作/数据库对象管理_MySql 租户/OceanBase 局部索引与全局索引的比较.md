---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 局部索引与全局索引的比较/","dgPassFrontmatter":true}
---


### OceanBase 局部索引与全局索引的比较
#### 1 OceanBase 局部索引，全局索引概述

##### 1.1 概述
与`局部索引`相比，由于`全局索引`有独立的分区规则，因此索引表中一个分区的索引值可能对应着主表的多个分区内的数据。由于索引的分区规则和主表的分区规则不一定相同，因此在分布式环境中，索引数据和主表数据存储的位置也无法保证始终在一起，这不可避免的会引入读写的 RPC 代价和分布式事务的代价；

例如：当主表的分区和_全局索引_ 的分区不在同一个物理位置上，`TABLE SCAN` 算子执行全局索引表扫描时就会包含一次 RPC 操作到远端机器上去获取主表数据。因此_全局索引_ 相比_局部索引_ 有更高的维护代价，您应当充分评估主表的分区规则，合理的选择分区键，尽量使更多的查询条件能够覆盖主表的分区键，从而尽可能的避免使用全局索引；

##### 1.2 局部索引简介
`局部索引`又名*分区索引*，创建索引的分区关键字是 `LOCAL`，分区键等同于表的分区键，分区数等同于表的分区数，总之，局部索引的分区机制和表的分区机制一样；

*局部索引* 是针对单个分区上的数据创建的索引，因此局部索引的索引键值跟表中的数据是一一对应的关系，即局部索引上的一个分区一定对应到一个表分区，它们具有相同的分区规则，因此对于局部唯一索引而言，它只能保证分区内部的唯一性，而无法保证表数据的全局唯一性；

如果要使用局部唯一索引去对数据唯一性做约束，那么局部唯一索引中必须包含表分区键；

> [!NOTE] 注意：
> 1. 在 Oracle 模式中，如果索引属性关键字没有指定，那么默认的索引属性是 `GLOBAL` 属性，即创建的索引是全局索引，并且索引表只有一个分区；
> 2. 在 MySql 模式中，如果索引属性关键字没有指定，那么默认的索引属性是 `LOCAL` 属性，即创建的索引是局部索引；
> 3. 唯一索引必须包括表分区函数中的所有列；


##### 1.3 全局索引简介
`全局索引`的创建规则是在索引属性中指定 `GLOBAL` 关键字；与局部索引相比，*全局索引最大的特点是全局索引的分区规则跟表分区是相互独立的*，分区表的全局索引允许指定自己的分区规则和分区个数，不一定需要跟表分区规则保持一致；

更进一步，既可以选择非分区模式也可以选择分区模式 ：`全局非分区索引`（Global Non-Partitioned Index），`全局分区索引`（Global Partitioned Index）；

如果主表没有指定分区键或者指定的分区数为 1，那么主表也只有一个分区，这个时候，全局索引的数据和主表数据的物理位置是相互绑定在一起的，无论是迁移还是副本 Leader 发生切换，它们都是作为一个整体进行变换，不会存在中间状态；

如果全局索引的分区规则和主表的分区规则相同并且分区数相同，这时建议创建一个局部索引。 一方面是因为全局索引的维护代价更大；另一方面是因为全局索引无法保证和主表分区的物理位置相同；

> [!NOTE] 注意：
> 1. 在 OceanBase 数据库的 Oracle 模式中，如果索引属性关键字没有指定，则默认的索引属性是 `GLOBAL` 属性，即创建的索引为全局索引，且索引表只有一个分区；
> 2. 全局索引的分区规则不一定要与表分区规则保持一致；
> 3. 如果全局索引的分区规则和主表的分区规则相同并且分区数相同，建议创建一个局部索引。 一方面是因为全局索引的维护代价更大；另一方面是因为全局索引无法保证和主表分区的物理位置相同，除非将其和主表指定在一个表组中；


#### 2 局部索引与全局索引的执行计划的比较
局部索引：
```sql
create table t_p_hash (c1 varchar(20),c2 int, c3 varchar(20)) partition by hash(c2) partitions 3;
create index idx_t_p_hash_c1 on t_p_hash (c1) local;
create index idx_t_p_hash_c3 on t_p_hash (c3) local;

explain extended select c1,c2 from t_p_hash where c3='100';
======================================================================
|ID|OPERATOR |NAME |EST. ROWS|COST |
----------------------------------------------------------------------
|0 |PX COORDINATOR | |2970 |19207|
|1 | EXCHANGE OUT DISTR |:EX10000 |2970 |18364|
|2 | PX PARTITION ITERATOR| |2970 |18364|
|3 | TABLE SCAN|t_p_hash(idx_t_p_hash_c3)|2970 |18364|
======================================================================
Outputs & filters:
-------------------------------------
0 - output([t_p_hash.c1(0x7f8b99cb4370)], [t_p_hash.c2(0x7f8b99cb30a0)]), filter(nil)
1 - output([t_p_hash.c2(0x7f8b99cb30a0)], [t_p_hash.c1(0x7f8b99cb4370)]), filter(nil), dop=1
2 - output([t_p_hash.c2(0x7f8b99cb30a0)], [t_p_hash.c1(0x7f8b99cb4370)]), filter(nil)
3 - output([t_p_hash.c2(0x7f8b99cb30a0)], [t_p_hash.c1(0x7f8b99cb4370)]), filter(nil),
	access([t_p_hash.c2(0x7f8b99cb30a0)], [t_p_hash.c1(0x7f8b99cb4370)]), partitions(p[0-2]),
	is_index_back=true, range_key([t_p_hash.c3(0x7f8b99cb3e50)], [t_p_hash.c2(0x7f8b99cb30a0)],
	[t_p_hash.__pk_increment(0x7f8b99cd9660)]), range(100,MIN,MIN ; 100,MAX,MAX),
	range_cond([t_p_hash.c3(0x7f8b99cb3e50) = '100'(0x7f8b99cb37f0)])

/*
t_p_hash(idx_t_p_hash_c3)：索引的回表逻辑是封装在 table scan 算子中；
partitions(p[0-2]：查询中如果没有指定分区键，那么局部索引将无法进行分区裁剪；
*/
```

全局索引：
```sql
create table t_p_key (c1 varchar(20),c2 int,c3 varchar(20)) partition by key (c2) partitions 3;
create unique index idx_t_p_key_c3_g on t_p_key (c3) global partition by key (c3) partitions 3;

explain extended select c1,c2 from t_p_key where c3='66';
==========================================================
|ID|OPERATOR |NAME |EST. ROWS|COST|
----------------------------------------------------------
|0 |TABLE LOOKUP|t_p_key |1 |91 |
|1 | TABLE SCAN |t_p_key(idx_t_p_key_c3_g)|1 |36 |
==========================================================
Outputs & filters:
-------------------------------------
0 - output([t_p_key.c1(0x7f8b99cb6050)], [t_p_key.c2(0x7f8b99cb3a80)]), filter(nil),partitions(p[0-2])
1 - output([t_p_key.c2(0x7f8b99cb3a80)], [t_p_key.__pk_increment(0x7f8b99cd9660)]), filter(nil),
	access([t_p_key.c2(0x7f8b99cb3a80)], [t_p_key.__pk_increment(0x7f8b99cd9660)]), partitions(p2),
	is_index_back=false, range_key([t_p_key.c3(0x7f8b99cb5010)], [t_p_key.shadow_pk_0(0x7f8b99cda660)],
	[t_p_key.shadow_pk_1(0x7f8b99cda8f0)]), range(66,MIN,MIN ; 66,MAX,MAX),
	range_cond([t_p_key.c3(0x7f8b99cb5010) = '66'(0x7f8b99cb5760)])

/*
where c3='66'：通过WHERE 条件裁剪出全局索引的分区 p2；
t_p_key(idx_t_p_key_c3_g)：对全局索引进行 table scan 操作得到对应的主键；
partitions(p2)：利用 table lookup 算子对主表进行精确的分区扫描，以避免扫描主表的所有分区；
*/
```


#### 3 局部索引与全局索引的取舍

1. 如果查询条件里“包含完整的分区键”，使用本地索引是最高效的；
2. 如果需要“不包含完整分区键”的唯一约束：
	1. 用全局索引；
	2. 或者本地索引，且需要索引列上必须带上表的分区键；
3. 其它情况，case by case：
	1. 通常来说，全局索引能为高频且精准命中的查询（比如单记录查询）提速并减少IO；对范围查询则不一定哪种索引效果更好；
	2. 不能忽视全局索引在DML语句中引入的额外开销：数据更新时带来的跨机分布式事务，事务的数据量越大则分布式事务越复杂；
4. 如果数据量较大，或者容易出现索引热点，可考虑创建全局分区索引


### 参考文档



