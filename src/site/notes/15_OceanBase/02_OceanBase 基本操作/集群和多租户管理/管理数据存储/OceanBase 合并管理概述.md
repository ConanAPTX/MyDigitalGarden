---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理数据存储/OceanBase 合并管理概述/","dgPassFrontmatter":true}
---


## OceanBase 合并管理概述
### 1 OceanBase 合并概述
与转储相比，合并通常是一个比较重的操作，时间也相对较长，在最佳实践中，一般期望在一天只做一次合并操作，并且控制在业务低峰期进行，因此有时也会把合并称之为每日合并；
合并操作(Major Compaction)是将动静态数据做归并，会比较费时。当转储产生的增量数据积累到一定程度时，通过 Major Freeze 实现大版本的合并。它和转储最大区别在于，合并是集群上所有的分区在一个统一的快照点和全局静态数据进行合并的行为，是一个全局的操作，最终形成一个全局快照；
合并，合并也就是 Major Compaction，在 OceanBase 数据库中也叫每日合并。顾名思义，这个概念诞生之初是希望这个动作放到每天凌晨 2 点左右整个集群做一次整体的 Compaction 动作；
合并一般是由 RS 根据写入状态或者用户设置发起调度，每次合并都会选取一个全局的快照点，集群内所有的分区都会用这个快照点的数据做一次 Major Compaction，这样每次合并集群所有的数据都基于这个统一的快照点生成相应的 SSTable，通过这个机制不仅能帮助用户定期整合增量数据，提升读取性能，同时还提供了一个天然的数据校验点，通过全局的一致位点，OceanBase 数据库能够在内部对多副本以及主表索引表进行多维度的物理数据校验；

#### 1.1 合并的实现
在 4.x 版本之前：
启动合并：RS 发起合并之后，通过心跳发送到 observer，observer 在收到心跳后查询 `__all_zone` 表判断是否能做合并，然后开始 observer 的合并流程；
合并中：observer 遍历所有的 partition，依次发起合并任务并交由后台线程池执行；
合并结束：observer 周期地遍历所有的 partition 来判断是否合并完成，对于完成的 partition 更新其 meta 信息和 checksum 信息到内部表，RS 周期地检查内部表，当所有的 partition 的 meta 都推到了指定版本，判断为合并结束；

#### 1.2 合并方式
OceanBase 合并方式有：`全量合并`，`增量合并`，`渐进合并`，其合并方式可以通过表级属性 *progressive_merge_num* 来控制合并行为，也可以通过 *default_progressive_merge_num* 配置项，来设置新建一张表时默认的逐渐合并次数；
当您未对新建表指定 progressive_merge_num 属性时，系统会将表级属性 progressive_merge_num 的值设置为 default_progressive_merge_num 的值；详细情况：[如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；

##### 1.2.1 全量合并
*全量合并，将静态数据全部读出并和动态数据合并为最终的静态数据。合并时间长，耗费 IO 和 CPU；*
`全量合并`是 OceanBase 数据库最初的合并算法，和 HBase 与 RocksDB 的 major compaction 过程是类似的。在全量合并过程中，会把当前的静态数据都读取出来，和内存中的动态数据合并后，再写到磁盘上去作为新的静态数据。在这个过程中，会把所有数据都重写一遍；
全量合并会极大的耗费磁盘 IO 和空间，除了 DBA 强制指定外，目前 OceanBase 数据库一般不会主动做全量合并；

##### 1.2.2 增量合并
`增量合并`，*仅仅合并被修改过的宏块，没有改变的宏块进行复用。增量合并极大地减少了合并的工作量，是 OceanBase 数据库目前默认的合并算法；*
在 OceanBase 数据库的存储引擎中，宏块是 OceanBase 数据库基本的 IO 写入单位，在很多情况下，并不是所有的宏块都会被修改，当一个宏块没有增量修改时，合并可以直接重用这个数据宏块，OceanBase 数据库中将这种合并方式称之为增量合并；增量合并极大地减少了合并的工作量，是OceanBase 数据库目前默认的合并算法。更进一步地，OceanBase 数据库会在宏块内部将数据拆分为更小的微块，很多情况下，也并不是所有的微块都会被修改，可以重用微块而不是重写微块。微块级增量合并进一步减少了合并的时间；

##### 1.2.3 渐进合并
`渐进合并`，*每次全量合并一部分，若干轮次后整体数据被重写一遍；*
为了支持业务的快速发展，用户不可避免地要做加列、减列、建索引等诸多 DDL 变更。这些 DDL 变更对于数据库来说通常是很昂贵的操作。MySQL 在很长一段时间内都不能支持在线的 DDL 变更（直到 5.6 版本才开始对 Online DDL 有比较好的支持），而即使到今天，对于 DBA 来说，在 MySQL 5.7 中做 Online DDL 仍然是一件比较有风险的操作，因为一个大的 DDL 变更就会导致 MySQL 主备间的 replication lag；
OceanBase 数据库在设计之初就考虑到了 Online DDL 的需求，目前在 OceanBase 数据库中加列、减列、建索引等 DDL 操作都是不阻塞读写的，也不会影响到多副本间的 paxos 同步。加减列的 DDL 变更是实时生效的，将对存储数据的变更延后到每日合并的时候来做。然而对于某些 DDL 操作如加减列等，是需要将所有数据重写一遍的，如果在一次每日合并过程中完成对所有数据的重写，那么对存储空间和合并时间都会是一个比较大的考验。
为了解决这个问题，OceanBase 数据库引入了渐进合并，将 DDL 变更造成的数据重写分散到多次每日合并中去做，渐进合并由 schema 中的 progressive_merge_num 属性来决定渐进的轮次；假设 progressive_merge_num =  60，那么一次合并就只会重写 60 分之一的数据，在 60 轮合并过后，数据就被整体重写了一遍。渐进合并减轻了 DBA 做 DDL 操作的负担，同时也使得 DDL 变更更加平滑；
schema 中的 progressive_merge_round 表示本次合并所处的渐进合并轮次；
当 progressive_merge_num = 1 时会发生全量合并，另外如果发生了 DDL 对于存储层列类型的变更，也会在下一轮合并中重写掉 major sstable；

##### 1.2.4 并行合并
`并行合并`，*将数据划分到不同线程中并行做合并*；
在 OceanBase 数据库 V1.0 中增加了对分区表的支持。对于不同的数据分区，合并是会并行来做的。但是由于数据倾斜，某些分区的数据量可能非常大。尽管增量合并极大减少了合并的数据量，对于一些更新频繁的业务，合并的数据量仍然非常大，为此 OceanBase 数据库引入了分区内并行合并。合并会将数据划分到不同线程中并行做合并，极大地提升了合并速度；
并行 compaction 是指将一个 compaction 任务按照 key_range 拆成若干的子任务，将任务交给不同的线程执行，加快 compaction 的速度；

##### 1.2.5 轮转合并
`在 V4.0.0 版本中取消了轮转合并；`
`轮转合并`，*在合并时将正在进行合并的副本上的流量切换到其他未合并的副本上，待该副本合并完成后再将流量切回。您可以对集群的每个 Zone 进行轮转合并，保证未合并的 Zone 可以完全无任何性能影响地对外提供服务；*
一般来说每日合并会在业务低峰期进行，但并不是所有业务都有业务低峰期。在每日合并期间，会消耗比较多的 CPU 和 IO，此时如果有大量业务请求，势必会对业务造成影响。为了规避每日合并对业务的影响。OceanBase 数据库借助多副本分布式架构引入了轮转合并的机制；
一般配置下，OceanBase 数据库会同时有 3 个数据副本，当一个数据副本在进行合并时，会将这个副本上的查询流量切到其他没在合并的集群上面，这样业务的查询就不受每日合并的影响。等这个副本合并完成后，再将查询流量切回来，继续做其他副本的合并。为了避免流量切过去后，cache 较冷造成的 rt 波动，在流量切换之前， OceanBase 数据库还会做 cache 的预热；

###### 1.2.5.1 相关配置项
enable_merge_by_turn：OB 的配置项，指示是否开启自动轮转合并；
merge_list / zone_merge_order：指定自动轮转合并的合并顺序，OB 1.3 之前的版本中用 `__all_zone` 表中的 merge_list 来表示，在 1.3 之后的版本中用配置项 zone_merge_order 来表示；
![9329f782513a8633f3c22cc9711429fc.png|600](/img/user/15_OceanBase/02_OceanBase%20%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/%E9%9B%86%E7%BE%A4%E5%92%8C%E5%A4%9A%E7%A7%9F%E6%88%B7%E7%AE%A1%E7%90%86/01_attachments/9329f782513a8633f3c22cc9711429fc.png)


> [!NOTE] `注意事项`：
> 1. 轮转合并期间，目标切主机器 stop，会导致卡合并吗？会的，stop 的机器不会跳过合并检查；
> 2. 轮转合并的时候是不是不能有机器被 stop？是的；
> 3. 关闭轮转合并还有这样的问题吗？
> 	1. 关闭轮转合并就避免了切主，但是 stop 的机器还是不能跳过合并检查，机器下线可以跳过合并；
   
#### 1.3 修改 OceanBase 数据库的合并方式
可以通过表级属性 *progressive_merge_num* 来控制合并行为，也可以通过 *default_progressive_merge_num* 配置项，来设置新建一张表时默认的逐渐合并次数；当您未对新建表指定 *progressive_merge_num* 属性时，系统会将表级属性 *progressive_merge_num* 的值设置为 *default_progressive_merge_num* 的值；
修改合并方式详情：[如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；

```sql
-- 查询配置项
SHOW PARAMETERS LIKE 'default_progressive_merge_num';
SHOW PARAMETERS WHERE name = 'default_progressive_merge_num';	-- 查询租户级配置项

-- 修改 OceanBase 配置项
ALTER SYSTEM SET default_progressive_merge_num = 0;
ALTER SYSTEM SET default_progressive_merge_num = 0 TENANT='ALL';	  -- 在系统租户下，修改所有租户级配置项
ALTER SYSTEM SET default_progressive_merge_num = 0 TENANT='Oracle';	-- 在系统租户下，修改指定租户的租户级配置项
	-- 若值为 0（默认值），即执行渐进合并，且渐进合并的次数为 100；在生产环境一般默认即可；
	-- 若值为 1，即强制执行全量合并，不执行渐进合并；
	-- 若值为 = n，表示 X 轮合并重写完全部数据，每轮合并重写 1/X；


-- 查询表的合并方式

-- 修改表的合并方式
ALTER TABLE $table SET progressive_merge_num = 1;		-- 全量合并
ALTER TABLE $table SET progressive_merge_num = 0;		-- 增量合并
ALTER TABLE $table SET progressive_merge_num = $num;	-- 渐进合并
```

#### 1.4 合并的压缩算法
说明：更高的压缩率将更节约磁盘空间，但是也意味着性能的牺牲。例如，通常 ZSTD 比 LZ4 节省更多的空间，但是 ZSTD 合并的时间更长，同时需要 IO 查询的 RT 也更大；
OceanBase 数据库不会实时将小部分数据刷盘，而是通过合并的方式集中对数据进行刷盘，因此可以采用压缩的方式来写入磁盘，此时磁盘的空间利用率得到提升。在压缩算法和压缩功能的选择上，您可以根据实际情况选择高压缩率但是耗费更多 CPU 的方式，也可以选择普通的压缩方式；
您可以通过参数 default_compress_func 来配置压缩方式，默认值为 zstd_1.0。其他可指定的值还有 none、lz4_1.0、snappy_1.0、zlib_1.0 和 zstd_1.0；
如果希望为单独的表选择特别的压缩算法，您可以在创建数据表时指定压缩算法；
MySQL 租户创建表并指定压缩算法的语法请参见 [CREATE TABLE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356929)；Oracle 租户创建表并指定压缩算法的语法请参见 [CREATE TABLE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357361)；

### 2 合并的触发方法，合并管理
OceanBase 数据库支持合并触发方式：`自动触发`、`定时触发`、`手动触发`；

#### 2.1 合并参数设置
```sql
-- 查询参数设置配置(使用 root 用户登录到数据库的 sys 租户)
SHOW PARAMETERS LIKE 'major_freeze_duty_time';

-- 修改合并配置
ALTER SYSTEM SET major_freeze_duty_time='03:00';
```

#### 2.2 前提条件
触发合并前，需要确保全局合并开关已开启，全局合并开关由集群级配置项 enable_major_freeze 控制，默认值为 True ，表示开启合并开关；
```sql
--1.使用 root 用户登录到数据库的 sys 租户

--2.通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'enable_major_freeze';

--3.是否开启自动全局冻结的功能
ALTER SYSTEM SET enable_major_freeze='True';    -- 开启
ALTER SYSTEM SET enable_major_freeze='False';   -- 关闭
```

#### 2.3 自动触发
合并可以通过自动触发来完成；
自动触发合并的条件是，当集群中任一租户的 MemTable 的内存使用达到了 freeze_trigger_percentage 设置的阈值，且转储次数已达到了 minor_freeze_times 设置的上限时，此时不会进行转储，就会自动触发整个集群的合并；
更多合并相关参数的说明及设置请参见：[修改转储配置](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355958)，；

#### 2.4 定时触发
##### 2.4.1 定时触发条件
可以通过指定每日合并触发时间来定时触发合并；
合并的定时触发条件是，每当系统时间达到了每日合并的时间点时，就会自动触发合并；每日合并的时间点通过配置项 major_freeze_duty_time 来控制，您也可以在 OCP 上修改，默认是每天 02:00 进行合并；

##### 2.4.2 通过 SQL 语句指定每日合并触发时间
```sql
-- 通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'major_freeze_duty_time';

-- 修改每日合并的触发时间(使用 root用户登录数据库的 sys 租户)
ALTER SYSTEM SET major_freeze_duty_time='01:00'; 	--修改所有租户每日合并的触发时间
ALTER SYSTEM SET major_freeze_duty_time='01:00' TENANT=tenant1，tenant2;  --修改指定租户每日合并的触发时间
```

##### 2.4.3 通过 OCP 设置每日合并触发时间

#### 2.5 手动触发合并
##### 2.5.1 通过 SQL 语句发起合并
合并也可以通过手动触发来完成；
```sql
-- 可以在"root@sys"用户下，通过以下命令发起手动合并（忽略当前MemStore的使用率）
-- 发起集群合并(使用 root 用户登录到数据库的 sys 租户)
ALTER SYSTEM MAJOR FREEZE;
```
##### 2.5.2 通过 OCP 手动发起合并

#### 2.6 手动控制合并
通过 MAJOR FREEZE 命令触发合并后，默认情况下，系统会自动调度合并任务，按照您指定的合并策略进行合并操作，例如，您可以打开轮转合并开关，系统会自动调度按 Zone 轮转合并；
OceanBase 数据库还提供了手动控制合并的方法，让用户手动控制合并流程；
##### 2.6.1 前提条件
手动控制合并前，请确认已通过 MAJOR FREEZE 命令触发合并，手动触发合并的详细操作，请参见 [手动触发合并](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355962)；
##### 2.6.2 开启手动控制合并开关
配置项 enable_manual_merge 用于控制是否开启手动控制合并，默认值为 False ，表示关闭手动控制合并开关，该配置项的更多信息，请参见 [enable_manual_merge](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355721)，；
```sql
-- 开启手动控制合并开关(使用 root 用户登录到数据库的 sys 租户)
ALTER SYSTEM SET enable_manual_merge='True';
```
##### 2.6.3 开始，暂停合并
```sql
/*
语法：
	ALTER SYSTEM START MERGE ZONE [=] 'zone_name';     	-- 开始合并
	ALTER SYSTEM SUSPEND MERGE [ZONE [=] 'zone_name'];	-- 暂停合并
	ALTER SYSTEM RESUME MERGE [ZONE [=] 'zone_name'];   -- 恢复合并
参数：
	[=]：表示等号（=）为可选；
	zone_name：表示准备开始合并的 Zone，多个 Zone 之间用英文逗号（,）分隔；
*/
-- 开始合并
ALTER SYSTEM START MERGE ZONE = 'zone1';

-- 暂停正在进行中的合并任务；【注意：该操作是高危操作】
ALTER SYSTEM SUSPEND MERGE;
ALTER SYSTEM SUSPEND MERGE ZONE = 'zone1';

-- 暂停合并后，也可以恢复合并
ALTER SYSTEM RESUME MERGE;
ALTER SYSTEM RESUME MERGE ZONE = 'zone1';

-- 清理合并的报错
ALTER SYSTEM clear merge error;
```


## 参考文档：
1. [存储架构概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354478)，介绍 OB 存储架构，及存储引擎的功能：数据存储，转储合并，查询读写，数据校验；
	   1. [转储和合并](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355112)，转储和合并概述；
2. [管理数据存储](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括转储管理，合并管理，数据压缩；
3. [如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；






