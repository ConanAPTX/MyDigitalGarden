---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/OceanBase 弱一致性读/","dgPassFrontmatter":true}
---


### OceanBase 弱一致性读

在分布式系统中，数据天然的多副本特性决定了数据读取的多种一致性读。强一致性读保证读操作的可线性化，即总是可以读到最新版本的教据；相对的，弱一致性读则无法保证在每一次读取的时候，一定能够读到最新的数据；

OceanBase 是基于 Multi-Paxos 的分布式数据库，数据以多副本的形式保存在不同的 Zone 或者节点。在数据更新的时候，主副本负责承相修改语句的执行，同时同步 Clog 提交日志到其他副本节点，只要包括主副本在内的多数派副本的日志落盘，事务就算提交成功，在这个过程中，一定有除主副本之外的其他副本节点进行了日志持久化，保证了容灾的能力；同时，由于主副本之外多数派节点并不保证日志落备以后，完成备副本的更新回放，因此，相比主副本的数据来说，可能存在落后的数据状态的副本；

OceanBase 数据库提供了两种一致性级别（Consistency Level)：`STRONG` 和 `WEAK`；
`STRONG`：指强一致性，读取最新数据，请求路由给主副本；`WEAK`：指弱一致性，不要求读取最新数据，请求优先路由给备副本；
OceanBase 数据库的写操作始终是强一致性的，即始终由主副本提供服务；读操作默认是强一致性的，由主副本提供服务，用户也可以指定弱一致性读，由备副本优先提供服务；


#### 1 弱一致性读配置项
##### 1.1 2.2.x 之前版本
详细情况：[弱一致性读_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355270)，；

##### 1.2 2.2.x 及以后版本
OceanBase 数据库弱一致性读分为两大类：*单调读*，*非单调读*；
不同的弱一致性读请求会路由到不同副本上，不同副本上读到的数据即使保证了有界旧一致性读，却无法保证副本之间数据的新旧，这对于反映人类顺序行为的数据库更新是不可接受的，因此，OceanBase 为 *弱一致性读* 提供了 *单调读* 的选项，通过设置 `enable_monotonic_weak_read` 配置项来设置；
若开启*单调读*，如果进程已经看到过数据对象的某个值，那么任何后续访问都不会返回在那个值之前的值；这样，在用户能够接受的数据弱一致性读的同时，也实现了弱一致性读的版本单调性；

在某些数据访问时效不敏感的业务中，通过从主副本写入数据，备副本读取，可以实现数据的读写分离，从而充分利用资源，提高数据库读取运行效率。在这个场景下，备副本的数据访问属于弱一致性读，为了便于对数据读取延迟的控制，OceanBase 数据库弱一致性读提供有界旧保证，即保证读到的数据最多落后 `max_stale_time_for_weak_consistency` 时间，默认配置值是 5 秒，支持租户级配置；如果备集群落后超过阈值，则读操作会重试等待，直到超时或青追上(备集群），或者选择其他副本(主集群)；

正常情况下，各个分区的备副本落后时间在 100 毫秒到 200 毫秒，弱一致性读的时效性在百毫秒级别；当出现网络抖动、无主等情况，弱一致性读的时效性会降低，一旦一个副本落后时间超过 `max_stale_time_for_weak_consistency`，该副本将不可读，内部重试机制会重试其他有效副本；如果所有副本都不可读，则持续重试，直到语句超时；

当开启*单调读* 开关后，OceanBase 数据库内部会为每个租户维护一个 Cluster 级别的弱一致性读版本号，该版本号也满足 `max_stale_time_for_weak_consistency` 约束。它的生成方式是统计租户下所有分区副本回放进度的最小值，如果某些分区副本落后时间超过 `max_stale_time_for_weak_consistency`，则不统计该副本。目前机制下，一个落后的副本会影响整体单调读的版本号，例如有两个分区的两个副本，一个副本落后 100ms，一个副本落后 1 秒，那么整体的单调读版本号是 1 秒。我们认为副本长时间落后不会是常态，正常情况下，单调读版本号都应该在百毫秒级别；

1. `weak_read_version_refresh_interval`：用于设置弱一致性读版本号的刷新周期，影响弱一致性读数据的延时；  
	1. 【集群级】【时间类型】；默认值为 50ms，取值范围为：`[0ms, +∞)`；动态生效； 
	3. 弱一致性读版本号刷新周期影响读取数据的新旧程度，它配置的值不能大于 `max_stale_time_for_weak_consistency`；
	5. 说明：若该配置项的值为 0 时，弱一致性单调读功能关闭，即不再维护 Cluster 级别弱一致性读版本号，表示不再刷新弱一致性读版本号，不提供单调读功能；
2. `enable_monotonic_weak_read`：用于设置是否开启单调读；  
	1. 【租户级】【bool】；默认值为：True，取值范围为：[True, False]；动态生效；  
	2. 弱读场景下(包括备库弱读)，开启该参数保证每次弱读结果的单调一致性；否则，可以关闭以提高性能；
	3. 弱一致性读会路由到不同副本上，不同副本上读到的数据新旧没有保证；单调读开关打开后，OceanBase 数据库保证读到的数据版本不回退，保证单调性； 一个典型的应用场景是保证因果序：两个事务 T1 和 T2，T1 提交之后，T2 才提交，如果客户端读到了 T2 事务的修改，那么之后一定可以读到 T1 事务的修改；
3. `max_stale_time_for_weak_consistency`：用于设置弱一致性读允许读到多旧的数据；  
	1. 【租户级】【时间类型】；默认值为 5s，取值范围为：[5s, +∞]；动态生效；  
	2. 默认值为 5s，表示弱一致性读最多允许读到最近 5s 内的数据；正常保持默认值即可，若是弱读场景且业务可以接受更大的数据延迟，可以按需调整；


#### 2 一致性级别的指定方式
在 OB 数据库中有 2 种方式指定一致性级别：

##### 2.1 通过 `ob_read_consistency` 系统变量指定

```sql
-- 设置 Session 变量，影响当前 Session
SHOW SESSION VARIABLES LIKE 'ob_read_consistency'; 
obclient> SET ob_read_consistency = WEAK;
obclient> SELECT * FROM t1;  -- 弱一致性读

-- 设置 Global 变量，影响之后新建的所有 Session
SHOW GLOBAL VARIABLES LIKE 'ob_read_consistency';
obclient> SET GLOBAL ob_read_consistency = STRONG;
```

##### 2.2 指定 Hint 方式
指定 `READ_CONSISTENCY` 的方式优先级高于 `ob_read_consistency`；

```sql
-- 通过指定 Hint 方式设置一致性级别
SELECT /*+READ_CONSISTENCY(WEAK) */ * FROM t1;   -- 指定 WEAK Consistency
SELECT /*+READ_CONSISTENCY(STRONG) */ * FROM t1; -- 指定 STRONG Consistency
```


> [!NOTE] SQL 语句的一致性级别：
> 1. 写语句 DML (INSERT/DELETE/UPDATE)：强制使用 *STRONG Consistency*，要求基于最新数据进行修改；
> 2. `SELECT FOR UPDATE`（SFU）：与写语句类似，强制使用 *STRONG Consistency*；
> 3. 只读语句 `SELECT`：用户可以配置不同的 *Consistency Level*，满足不同的读取需求；


#### 3 事务的一致性级别

弱一致性读的最佳实践是为不在事务中的 SELECT 语句指定 WEAK 一致性级别，它的语义是确定的。对于显式开启事务的场景，语法上，OceanBase 数据库允许不同的语句配置不同的一致性级别，这样会让用户很困惑，而且如果使用不当，SQL 会报错；


> [!NOTE] 原则：
> 1. 一致性级别是事务级的，事务内所有语句采用相同的一致性级别；
> 2. 由事务的第一条语句决定事务的一致性级别，后续的 SELECT 语句如果指定了不同的一致性级别，则强制改写为事务的一致性级别；
> 3. 写语句和 SFU 语句只能采用 STRONG，如果事务级一致性级别为 WEAK，则报错 `OB_NOT_SUPPORTED`；


> [!NOTE] 对于单条 SQL 而言，一致性级别的确定规则优先级从大到小可以概括为：
> 1.  根据语句类型确定的一致性级别，例如 DML 和 SFU 必须采用 STRONG；
> 2.  事务的一致性级别，如果语句在事务中，而且不是第一条语句，则采用事务的一致性级别；
> 3.  通过 Hint 指定的一致性级别；
> 4.  系统变量指定的一致性级别；
> 5.  缺省采用 STRONG


```sql
BEGIN;
insert into t1 values (1);   -- 修改语句，consistency_level=STRONG，整个事务应该是 STRONG             

-- SQL自身的 consistency_level=WEAK，但由于第一条语句为 STRONG，因此这条语句的 consistency_level 强制设置为 STRONG
select /*+READ_CONSISTENCY(WEAK) */ * from t1;
COMMIT;

BEGIN;
select * from t1 for update;   -- SFU属于修改语句，consistency_level=STRONG，整个事务应该也是STRONG

-- SQL自身的consistency_level=WEAK，但由于第一条语句为STRONG，因此这条语句的consistency_level强制设置为STRONG
select /*+READ_CONSISTENCY(WEAK) */ * from t1;
COMMIT;

BEGIN;
select /*+READ_CONSISTENCY(WEAK) */ * from t1;  -- 第一条语句为WEAK
select * from t1;  -- 虽然本条语句为STRONG，但是会继承第一条语句的consistency level，会强制设置为WEAK
COMMIT;

BEGIN;
select /*+READ_CONSISTENCY(WEAK) */ * from t1;  -- 第一条语句为WEAK
insert into t1 values (1);  -- 修改语句，必须为STRONG，由于第一条语句为WEAK，这里会报错：NOT SUPPORTED
select * from t1 for update;  -- SFU属于修改语句，必须为STRONG，这里同样会报错：NOT SUPPORTED
COMMIT;
```


#### 4 与隔离级别关系
1. STRONG 支持所有的隔离级别；
2. WEAK 仅支持读已提交 `READ COMMITTED` 隔离级别，其他隔离级别下会报错 `OB_NOT_SUPPORTED`；


#### 5 时间戳生成方式

### 参考文档
1. [弱一致性读_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355270)，；



