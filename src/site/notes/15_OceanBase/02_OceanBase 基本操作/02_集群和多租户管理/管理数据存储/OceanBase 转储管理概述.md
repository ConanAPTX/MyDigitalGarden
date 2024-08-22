---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 转储管理概述/","dgPassFrontmatter":true}
---


### OceanBase 转储管理概述
#### 1 OceanBase 转储原理概述
OceanBase 数据库的存储引擎基于 LSM-Tree 架构，数据大体上被分为 *MemTable* 和 *SSTable* 两部分，当 MemTable 的大小超过一定阈值时，就需要将 MemTable 中的数据转存到 SSTable 中以释放内存，该过程称为`转储`；

在转储之前首先需要保证被转储的 MemTable 不再进行新的数据写入，该过程称为冻结(Minor Freeze)，冻结会阻止当前活跃的 MemTable 再有新的写入，同时会生成新的活跃 MemTable；

##### 1.1 转储，合并动作介绍
在 OceanBase 中的 Compaction 分为三种类型：`Mini Compaction`、`Minor Compaction`、`Major Compaction`；

###### 1.1.1 Mini Compaction，转储
Mini Compaction 是一种 Tiered 类型的 Compaction，核心就是释放内存和数据日志，内存中的 Frozen(冻结) MemTable 通过 Mini Compaction 变成磁盘上的 Mini SSTable；
Mini Compaction 在 OceanBase 设计里代表的就是一次转储，对应的类型是 MINI_MERGE；

###### 1.1.2 Minor Compaction，小合并
随着用户数据的写入，Mini SSTable 的数量会逐渐增多，在查询时需要访问的 SSTable 数量会增多，会影响查询的性能。Minor Compaction 就是将多个 Mini SSTable 合成一个，主要目的是减少 SSTable 的数量，减少读放大问题。当 Mini SSTable 的数量超过阈值时，后台会自动触发 Minor Compaction；
Minor Compaction 细分为两类：
`MINI_MINOR_MERGE`: L0 -> L0；Tiered 类型的 Compaction，将若干个 Mini SSTable 合成一个 Mini SSTable，放置于 L0 层。 对应的类型是 MINI_MINOR_MERGE；
`MINOR_MERGE`: L0 - > L1；Leveled 类型的 Compaction，将若干个 Mini SSTable 与 Minor SSTable 合成一个新的 Minor SSTable，放置于 L1 层。 对应的类型是 MINOR_MERGE；

###### 1.1.3 Major Compaction，大合并
Major Compaction 指的是大合并；
 
##### 1.2 分层转储
在 OceanBase 数据库 V2.1.x 及之前的版本，OceanBase 数据库在同一时刻只会维护一个转储 SSTable，当 MemTable 需要进行转储时，会将 MemTable 中的数据与转储 SSTable 中的数据进行归并。随着转储次数不断增多，转储 SSTable 的大小也越来越大，而一次转储需要操作的数据量也越来越多，导致转储速度越来越慢，进而可能导致 MemTable 内存不足；

从 V2.2.x 版本开始，OceanBase 数据库引入了分层转储策略，在原有的基础上增加了 L0 层，即 OceanBase 数据库的转储分为以下三层：`L0 层(Mini SSTable)`，`L1 层(Minor SSTable)`，`L2 层(Major SSTable)`；

分层转储的概念图如下所示：![OB 转储，合并_001.png](/img/user/02_%E9%99%84%E4%BB%B6/Image/15_OceanBase/OB%20%E8%BD%AC%E5%82%A8%EF%BC%8C%E5%90%88%E5%B9%B6_001.png)

分层转储相关的配置项：`minor_compact_trigger`，`__minor_compaction_amplification_factor`，`major_compact_trigger`；

###### 1.2.1 L0 层，Mini SSTable
*被冻结的 MemTable 会直接 flush 为 Mini SSTable*；
在 OceanBase 数据库中，被冻结的 MemTable 会直接 Flush 为 Mini SSTable，L0 层内部称为 Mini SSTable，根据不同转储策略需要的不同参数设置，L0 层 SSTable 可能存在，也可以为空；在 L0 层内部支持下压，但必须选择 Version 值域连续的 SSTable；

*OceanBase 数据库内部可以同时存在多个 Mini SSTable；*
对于 L0 层提供 server 级配置参数来设置 L0 层内部分层数和每层最大 SSTable 个数，L0 层内部即分为level-0 到 level-n 层，每层最大容纳 SSTable 个数相同。当 L0 层 level-n 的 SSTable 到达一定数目上限或阈值后开始整体 compaction，合并成一个 SSTable 写入 level-n+1 层。当 L0 层 max level 内 SSTable 个数达到上限后，开始做 L0 层到 L1 层的整体 compaction 释放空间；

*L0 层内部支持下压，但必须选择 version 值域连续的 SSTable；*
在存在 L0 层的转储策略下，冻结 MemTable 直接转储在 L0-level0 写入一个新的 Mini SSTable，L0 层每个 level 内多个 SSTable 根据 base_version 有序，后续本层或跨层合并时需要保持一个原则，参与合并的所有 SSTable 的 version 必须邻接，这样新合并后的 SSTable 之间仍然能维持 version 有序，简化后续读取合并逻辑；
L0 层内部分层会延缓到 L1 的 compaction，更好的降低写放大，但同时会带来读放大，假设共 n 层，每层最多 m 个 SSTable，则最差情况 L0 层会需要持有（n X m + 2）个 SSTable，因此实际应用中层数和每层 SSTable 上限都需要控制在合理范围；

1. `minor_compact_trigger`：控制L0层 Mini SSTable 总数；
2. `major_compact_trigger`：控制 memtable dump flush 次数达到时触发 major compaction；


###### 1.2.2 L1 层，Minor SSTable
*大多数情况下仅有一个 Minor SSTable；每次下压都会在 L1 层生成新的 Minor SSTable，代替原有的 Minor SSTable；*
L1 层内部称为 Minor SSTable，L1 层的 Minor SSTable 仍然维持 rowkey 有序，每当 L0 层 Mini SSTable 达到 compaction 阈值后，L1 层 Minor SSTable 开始参与和 L0 层的 compaction。为了尽可能提升 L1 Compaction 效率, 降低整体写放大, OceanBase 数据库内部提供写放大系数设置, 当 L0 层 Mini SSTable 总大小和 L1 Minor SSTable 大小比率达到指定阈值后, 才开始调度 L1 Compaction, 否则仍调度 L0 层内部 Compaction；

###### 1.2.3 L2 层，Major SSTable
*基线数据，在合并时产生，且一般情况下仅有一个；*
L2 层是基线 Major SSTable，即是基线数据，为保持多副本间基线数据完全一致，日常转储过程中 Major SSTable 仍保持只读，不发生实际 compaction 动作；

##### 1.3 Queuing 表转储
在 LSM Tree 架构下，对数据行的删除并不是原地删除，而是通过写入一行 Delete 操作（tombstore）来标记删除，若某个用户业务表主要操作为频繁的插入与删除，从绝对数据量上来看可能并不大，但是由于 LSM Tree 的架构特征，会导致对这类表的增量数据中存在大量的 Delete 标记, 即使用户的实际扫描范围很小，但是 SSTable 内部无法快速识别这些数据是否删除，仍然要扫描大量包含删除标记的数据，导致一个小查询的响应时间可能远远超过用户预期。为了解决这类表的查询性能问题，OceanBase 数据库为用户特别提供了一种支持自定义的表模式，称为 **Queuing 表** （从业务角度有时候也会称为 **Buffer 表** ），对其实现了特殊的转储策略；

```sql
-- 指定 queuing 表模式
obclient> ALTER TABLE table_name TABLE_MODE = 'queuing';
```

从 2.2.7 版本开始，对于 Queuing 表，OB 数据库引入了一种自适应的 Buffer 表转储策略，由存储层在每次转储时根据转储的统计信息来自主判断是否需要对该表采用 Buffer 表转储策略，当发现一个表存在类似 Buffer 表行为时，系统会尝试对这个表做 Buffer Minor Merge 的调度，对这个表基于 Major SSTable 和最新的增量数据以当前的读快照时间生成一个 Buf Minor SSTable，这次合并动作会消除掉增量数据里的所有 Delete 标记，后续查询基于新生成的 Buf Minor SSTable 就可以避免原有的大量无效扫描动作；


#### 2 OceanBase 数据库转储管理
##### 2.1 自动触发转储 
租户在创建时指定了租户的内存大小，租户的内存分为 *动态可伸缩内存*  和 *MemTable*；

当一个租户的 MemTable 内存的使用量达到 *memstore_limit_percentage \* freeze_trigger_percentage* 所限制使用的值时，就会自动触发冻结(转储的前置动作)，然后系统内部会再调度转储；当转储达到一定的条件时，则可能会触发自动合并；

关于上述的集群的 2 个配置项，详细情况：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存#4.1 Memstore 内存管理相关配置项\|OceanBase 管理内存#4.1 Memstore 内存管理相关配置项]]，；

##### 2.2 手动触发转储
系统租户可以通过命令手动触发转储，手动转储支持租户级别、Zone 级别、Server 级别、日志流级别和 Tablet 级别；
```sql
/*
语法：
	ALTER SYSTEM MINOR FREEZE ZONE [=] zone_name [, zone_name ...];
	ALTER SYSTEM MINOR FREEZE TENANT [=] ALL | tenant_name [, tenant_name ...];
	ALTER SYSTEM MINOR FREEZE SERVER = ('ip:port' [, 'ip:port'...]);
	ALTER SYSTEM MINOR FREEZE PARTITION_ID [=] 'partition_id%partition_count@table_id' 
	ALTER SYSTEM MINOR FREEZE TENANT [=] tenant_name LS [=] ls_id;
*/
-- 1.使用 root 用户登录集群的 sys 租户
mysql -h 100.64.132.106 -P2883 -uroot@sys#zwy_cluster_20220718 -p -A -c -Doceanbase
	-- 输入密码

-- 2.直接对整个集群进行转储
ALTER SYSTEM MINOR FREEZE; 		-- 直接对整个集群进行转储

-- 3.对指定的一个或多个 Zone 进行转储
ALTER SYSTEM MINOR FREEZE ZONE = zone1;  -- 对指定的一个或多个 Zone 进行转储

-- 4.对所有租户、指定的一个或多个租户进行转储
ALTER SYSTEM MINOR FREEZE TENANT = ALL;              -- 对所有租户进行转储
ALTER SYSTEM MINOR FREEZE TENANT = ('tenant_name');  -- 对 tenant1 进行转储
ALTER SYSTEM MINOR FREEZE TENANT = tenant1,tenant2;  -- 对 tenant1,tenant2 进行转储
-- 如果不指定租户，执行 ALTER SYSTEM MINOR FREEZE 语句，则表示对 sys 租户本身进行转储；

-- 5.对指定的一个或多个 OBServer 节点进行转储
ALTER SYSTEM MINOR FREEZE SERVER = ('xx.xx.xx.xx:2882');
ALTER SYSTEM MINOR FREEZE SERVER = ('10.10.10.1:2882');

-- 6.对指定的分区副本进行转储
ALTER SYSTEM MINOR FREEZE PARTITION_ID = '8%0@110061113945388';

-- 7.对指定租户的指定日志流进行转储
-- 对指定租户指定日志流中的指定 Tablet 进行转储；
-- ALTER SYSTEM MINOR FREEZE TENANT [=] tenant_name LS [=] ls_id TABLET_ID = tid;
ALTER SYSTEM MINOR FREEZE TENANT = t1 LS = 1001; -- 语句执行后，系统会对指定租户的指定日志流的所有 Tablet 进行转储；

-- 8.对指定租户的指定 Tablet 进行转储 
-- ALTER SYSTEM MINOR FREEZE TENANT [=] tenant_name TABLET_ID = tid;     
ALTER SYSTEM MINOR FREEZE TENANT = t1 tablet_id = 200001;
ALTER SYSTEM MINOR FREEZE TENANT = t1 LS = 1001 tablet_id = 200001;
/*
  tenant_name 为指定租户的租户名；
  ls_id 为指定租户的日志流 ID；
  tid 为指定日志流中的 Tablet ID；
  租户的日志流 ID 和 Tablet ID 可以通过查询 oceanbase.CDB_OB_TABLET_TO_LS 视图获取
*/	
```


### 参考文档：

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02//ocean-base/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 参考文档：
1. [存储架构概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354478)，介绍 OB 存储架构，及存储引擎的功能：数据存储，转储合并，查询读写，数据校验；
	 1. [转储和合并](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355112)，转储和合并概述；
2. [管理数据存储](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括转储管理，合并管理，数据压缩；
	1. [转储管理](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括自动触发转储，自动触发转储，手动触发转储，查询转储信息，修改转储配置；
	2. [OceanBase 数据库的分层转储功能](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000023?back=kb)，介绍 OceanBase 数据库的分层转储功能；
	3. [LSM-Tree 和 OceanBase 分层转储](https://my.oschina.net/actiontechoss/blog/8563823)，详细分层转储原理及相关参数介绍；
3. [如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；
4. *合并管理概述*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819293)，；
5. *自动触发合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819289)，；
6. *定时触发合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819290)，；
7. *自适应合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819294)，；
8. *手动触发合并*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946263)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886130)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819291)，；
9. *手动控制合并*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001417800)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001467935)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819292)，；
10. *查看合并信息*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945932)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886131)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819295)，；
	1. [如何查看 Compaction 的当前合并进度及历史记录](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209910?back=kb)，；
	2. [查询合并过程_v4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886131)，；
	3. [转储查看](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376777)，；


</div></div>






