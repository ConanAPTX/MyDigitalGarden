---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 合并管理概述/","dgPassFrontmatter":true}
---


### OceanBase 合并管理概述

与转储相比，合并通常是一个比较重的操作，时间也相对较长，在最佳实践中，一般期望在一天只做一次合并操作，并且控制在业务低峰期进行，因此有时也会把合并称之为*每日合并*；

合并操作(Major Compaction)是将动静态数据做归并，会比较费时。当转储产生的增量数据积累到一定程度时，通过 Major Freeze 实现大版本的合并。它和转储最大区别在于，合并是集群上所有的分区在一个统一的快照点和全局静态数据进行合并的行为，是一个全局的操作，最终形成一个全局快照；

合并，合并也就是 Major Compaction，在 OceanBase 数据库中也叫每日合并。顾名思义，这个概念诞生之初是希望这个动作放到每天凌晨 2 点左右整个集群做一次整体的 Compaction 动作；
合并一般是由 RS 根据写入状态或者用户设置发起调度，每次合并都会选取一个全局的快照点，集群内所有的分区都会用这个快照点的数据做一次 Major Compaction，这样每次合并集群所有的数据都基于这个统一的快照点生成相应的 SSTable，通过这个机制不仅能帮助用户定期整合增量数据，提升读取性能，同时还提供了一个天然的数据校验点，通过全局的一致位点，OceanBase 数据库能够在内部对多副本以及主表索引表进行多维度的物理数据校验；

#### 1 合并的实现
##### 1.1 在 4.x 版本之前
启动合并：RS 发起合并之后，通过心跳发送到 observer，observer 在收到心跳后查询 `__all_zone` 表判断是否能做合并，然后开始 observer 的合并流程；
合并中：observer 遍历所有的 partition，依次发起合并任务并交由后台线程池执行；
合并结束：observer 周期地遍历所有的 partition 来判断是否合并完成，对于完成的 partition 更新其 meta 信息和 checksum 信息到内部表，RS 周期地检查内部表，当所有的 partition 的 meta 都推到了指定版本，判断为合并结束；


#### 2 合并方式
OceanBase 合并方式有：`全量合并`，`增量合并`，`渐进合并`，其合并方式可以通过表级属性 *progressive_merge_num* 来控制合并行为，也可以通过 *default_progressive_merge_num* 配置项，来设置新建一张表时默认的逐渐合并次数；

当您未对新建表指定 progressive_merge_num 属性时，系统会将表级属性 progressive_merge_num 的值设置为 default_progressive_merge_num 的值；详细情况：[如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；

##### 2.1 全量合并
*全量合并，将静态数据全部读出并和动态数据合并为最终的静态数据。合并时间长，耗费 IO 和 CPU；*
`全量合并`是 OceanBase 数据库最初的合并算法，和 HBase 与 RocksDB 的 major compaction 过程是类似的。在全量合并过程中，会把当前的静态数据都读取出来，和内存中的动态数据合并后，再写到磁盘上去作为新的静态数据。在这个过程中，会把所有数据都重写一遍；
全量合并会极大的耗费磁盘 IO 和空间，除了 DBA 强制指定外，目前 OceanBase 数据库一般不会主动做全量合并；

##### 2.2 增量合并
`增量合并`，*仅仅合并被修改过的宏块，没有改变的宏块进行复用。增量合并极大地减少了合并的工作量，是 OceanBase 数据库目前默认的合并算法；*
在 OceanBase 数据库的存储引擎中，宏块是 OceanBase 数据库基本的 IO 写入单位，在很多情况下，并不是所有的宏块都会被修改，当一个宏块没有增量修改时，合并可以直接重用这个数据宏块，OceanBase 数据库中将这种合并方式称之为增量合并；增量合并极大地减少了合并的工作量，是OceanBase 数据库目前默认的合并算法。更进一步地，OceanBase 数据库会在宏块内部将数据拆分为更小的微块，很多情况下，也并不是所有的微块都会被修改，可以重用微块而不是重写微块。微块级增量合并进一步减少了合并的时间；

##### 2.3 渐进合并
`渐进合并`，*每次全量合并一部分，若干轮次后整体数据被重写一遍；*
为了支持业务的快速发展，用户不可避免地要做加列、减列、建索引等诸多 DDL 变更。这些 DDL 变更对于数据库来说通常是很昂贵的操作。MySQL 在很长一段时间内都不能支持在线的 DDL 变更（直到 5.6 版本才开始对 Online DDL 有比较好的支持），而即使到今天，对于 DBA 来说，在 MySQL 5.7 中做 Online DDL 仍然是一件比较有风险的操作，因为一个大的 DDL 变更就会导致 MySQL 主备间的 replication lag；
OceanBase 数据库在设计之初就考虑到了 Online DDL 的需求，目前在 OceanBase 数据库中加列、减列、建索引等 DDL 操作都是不阻塞读写的，也不会影响到多副本间的 paxos 同步。加减列的 DDL 变更是实时生效的，将对存储数据的变更延后到每日合并的时候来做。然而对于某些 DDL 操作如加减列等，是需要将所有数据重写一遍的，如果在一次每日合并过程中完成对所有数据的重写，那么对存储空间和合并时间都会是一个比较大的考验。
为了解决这个问题，OceanBase 数据库引入了渐进合并，将 DDL 变更造成的数据重写分散到多次每日合并中去做，渐进合并由 schema 中的 progressive_merge_num 属性来决定渐进的轮次；假设 progressive_merge_num =  60，那么一次合并就只会重写 60 分之一的数据，在 60 轮合并过后，数据就被整体重写了一遍。渐进合并减轻了 DBA 做 DDL 操作的负担，同时也使得 DDL 变更更加平滑；
schema 中的 progressive_merge_round 表示本次合并所处的渐进合并轮次；
当 progressive_merge_num = 1 时会发生全量合并，另外如果发生了 DDL 对于存储层列类型的变更，也会在下一轮合并中重写掉 major sstable；

##### 2.4 并行合并
`并行合并`，*将数据划分到不同线程中并行做合并*；
在 OceanBase 数据库 V1.0 中增加了对分区表的支持。对于不同的数据分区，合并是会并行来做的。但是由于数据倾斜，某些分区的数据量可能非常大。尽管增量合并极大减少了合并的数据量，对于一些更新频繁的业务，合并的数据量仍然非常大，为此 OceanBase 数据库引入了分区内并行合并。合并会将数据划分到不同线程中并行做合并，极大地提升了合并速度；
并行 compaction 是指将一个 compaction 任务按照 key_range 拆成若干的子任务，将任务交给不同的线程执行，加快 compaction 的速度；

##### 2.5 轮转合并
`在 V4.0.0 版本中取消了轮转合并；`
`轮转合并`，*在合并时将正在进行合并的副本上的流量切换到其他未合并的副本上，待该副本合并完成后再将流量切回。您可以对集群的每个 Zone 进行轮转合并，保证未合并的 Zone 可以完全无任何性能影响地对外提供服务；*
一般来说每日合并会在业务低峰期进行，但并不是所有业务都有业务低峰期。在每日合并期间，会消耗比较多的 CPU 和 IO，此时如果有大量业务请求，势必会对业务造成影响。为了规避每日合并对业务的影响。OceanBase 数据库借助多副本分布式架构引入了轮转合并的机制；
一般配置下，OceanBase 数据库会同时有 3 个数据副本，当一个数据副本在进行合并时，会将这个副本上的查询流量切到其他没在合并的集群上面，这样业务的查询就不受每日合并的影响。等这个副本合并完成后，再将查询流量切回来，继续做其他副本的合并。为了避免流量切过去后，cache 较冷造成的 rt 波动，在流量切换之前， OceanBase 数据库还会做 cache 的预热；

相关配置项：

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02//ocean-base/#3990a6" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



4. *轮转合并配置项* 

</div></div>


[[OB 转储，合并_002.png|600]]

> [!NOTE] `注意事项`：
> 1. 轮转合并期间，目标切主机器 stop，会导致卡合并吗？会的，stop 的机器不会跳过合并检查；
> 2. 轮转合并的时候是不是不能有机器被 stop？是的；
> 3. 关闭轮转合并还有这样的问题吗？
> 	1. 关闭轮转合并就避免了切主，但是 stop 的机器还是不能跳过合并检查，机器下线可以跳过合并；


#### 3 修改 OceanBase 数据库的合并方式
可以通过表级属性 *progressive_merge_num* 来控制合并行为，也可以通过 *default_progressive_merge_num* 配置项，来设置新建一张表时默认的逐渐合并次数；当您未对新建表指定 *progressive_merge_num* 属性时，系统会将表级属性 *progressive_merge_num* 的值设置为 *default_progressive_merge_num* 的值；
修改合并方式详情：[如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；

```sql
-- 查询配置项
SHOW PARAMETERS LIKE 'default_progressive_merge_num';
SHOW PARAMETERS WHERE name = 'default_progressive_merge_num';	-- 查询租户级配置项

-- 修改 OceanBase 配置项
ALTER SYSTEM SET default_progressive_merge_num = 0;
ALTER SYSTEM SET default_progressive_merge_num = 0 TENANT='ALL';	  -- 在系统租户下，修改所有租户级配置项
ALTER SYSTEM SET default_progressive_merge_num = 0 TENANT='Oracle';	  -- 在系统租户下，修改指定租户的租户级配置项
	-- 若值为 0（默认值），即执行渐进合并，且渐进合并的次数为 100；在生产环境一般默认即可；
	-- 若值为 1，即强制执行全量合并，不执行渐进合并；
	-- 若值为 = n，表示 X 轮合并重写完全部数据，每轮合并重写 1/X；

-- 查询表的合并方式

-- 修改表的合并方式
ALTER TABLE $table SET progressive_merge_num = 1;		-- 全量合并
ALTER TABLE $table SET progressive_merge_num = 0;		-- 增量合并
ALTER TABLE $table SET progressive_merge_num = $num;	-- 渐进合并
```


#### 4 合并的压缩算法
说明：更高的压缩率将更节约磁盘空间，但是也意味着性能的牺牲。例如，通常 ZSTD 比 LZ4 节省更多的空间，但是 ZSTD 合并的时间更长，同时需要 IO 查询的 RT 也更大；

OceanBase 数据库不会实时将小部分数据刷盘，而是通过合并的方式集中对数据进行刷盘，因此可以采用压缩的方式来写入磁盘，此时磁盘的空间利用率得到提升。在压缩算法和压缩功能的选择上，您可以根据实际情况选择高压缩率但是耗费更多 CPU 的方式，也可以选择普通的压缩方式；

您可以通过参数 default_compress_func 来配置压缩方式，默认值为 zstd_1.0。其他可指定的值还有 none、lz4_1.0、snappy_1.0、zlib_1.0 和 zstd_1.0；

如果希望为单独的表选择特别的压缩算法，您可以在创建数据表时指定压缩算法；

MySQL 租户创建表并指定压缩算法的语法请参见 [CREATE TABLE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356929)，；Oracle 租户创建表并指定压缩算法的语法请参见 [CREATE TABLE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357361)，；


#### 5 合并的触发方法
OceanBase 数据库支持：`自动触发合并`、`定时触发合并`、`自适应合并`，`手动触发合并`；

更多详细：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/合并的触发方式\|合并的触发方式]]，；


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
