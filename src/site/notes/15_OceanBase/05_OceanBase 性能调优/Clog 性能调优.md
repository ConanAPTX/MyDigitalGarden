---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Clog 性能调优/","dgPassFrontmatter":true}
---


### Clog 性能调优
#### 1 Clog 性能调优  
OceanBase Clog 的全称是 OceanBase Commit Log，是 OceanBase 日志服务的核心，主要服务于事务的提交，副本的同步和 Leader 选举等功能；在 OceanBase 事务提交中，分区的 Leader 副本和 Follower 副本组成了 Paxos 组将 Clog 日志从 Leader 副本同步到 Follower 节点，Follower 副本收到 Clog 日志后进行回放，从而达到 Leader 与 Follower 的数据一致；  
  
事务的提交要保证 Paxos 组内多数派副本的 Clog 同步成功，因此 Clog 落盘与 Clog 同步的延迟决定了事务提交的性能；OceanBase 分布式数据库为了提高 Clog 落盘，同步的性能，做了一系列的优化；  

##### 1.1 名词解释
`Clog 滑动窗口`：Clog 滑动窗口是以分区为单位进行管理的 Clog buffer；在分布式事务提交过程中，为了确认（confirm）日志在多数派中日志提交的状态和优化 Clog 在副本中的落盘过程，OceanBase 使用 Clog 滑动窗口来缓存还没有完成多数派确认的 Clog；当事务提交完成了 Clog 多数派落盘和本地落盘后，便可以将 Clog 从本地的滑动窗口中滑出；

`Clog 聚合`：Clog 聚合是把一个事务内属于同一分区的多条事务日志聚合成一条日志，进行日志的多数派提交，降低日志同步的开销；

`Clog Reconfirm`：Leader 改选后，新主上任时，需要再次确认同步成功的 Clog，确保原主提交的 Clog 完成了同步，这就是 Clog Reconfirm 流程；

`Clog Replay`：
在 OceanBase 节点宕机重启后，将已经确认的 Clog 会放到 Memtable 中，就是 Clog 会放的过程；
在 OceanBase 的分布式事务中，当事务提交完成，Clog 同步到多数派节点，Follower 节会将 Clog 回放到 Follower 副本的 Memstore 中提供对外的弱一致性读；

`Clog 主动卸主（Leader Revoke）`：当 OceanBase 的 Clog 模块有以下问题时，Leader 副本会主动卸任：
Leader 副本上任时 reconfirm Clog 超时了，日志关键字会打印 "*clog reconfirm timeout*"；
Leader 副本同步一条 Clog 超时，未达成多数派，日志关键字会打印 "*clog sliding_window timeout*"；
Leader 副本上任过程中状态切换超时了，日志关键字为 "*clog role change timeout*"；

`副本 Rebuild`：如果 Follower 与 Leader 的 Clog 同步产生了比较大的延迟，Follower 副本会 copy Leader 副本的 sstable 来重建基线，然后再通过 Clog 追平数据，这个过程就是副本的 rebuild；

##### 1.2 相关参数
1. `_clog_aggregation_buffer_amount`：Clog 事务日志聚合 Buffer 个数（每个 buffer 大小 64K）；
	1. 【*租户级*】，默认值为 0；
2. `_flush_clog_aggregation_buffer_timeout`：控制刷新 Clog 日志聚合 Buffer 的超时时间；
	1. 【*租户级*】，默认值为 0ms；
3. `clog_max_unconfirmed_log_count`：用于设置事务模块中未确认日志的最大数量；每个分区的 Clog 滑动窗口大小（日志记录数）；
	1. 【*租户级*】【整型】；默认值为 1500，取值范围为：[100, 50000]；动态配置项，修改后直接生效；
	2. Clog 日志滑动窗口的大小，用于设置事务模块中未确认日志的最大数量；
	3. 建议保持默认值；在 DML 并发度比较高的业务场景，增大Clog滑动窗口，提升事务处理，提交的性能；特别地，对于内存小的主机(建议设置为 5000)，提升 clog 分配效率；
4. `enable_clog_persistence_compress`：用于设置是否开启事务日志落盘压缩；*从 V4.0.0 版本开始弃用*；clog 存储压缩的开关；
	1. 【*租户级*】【字符串】；默认值为 false；动态配置项，修改后直接生效；
	2. 开启 clog 存储压缩，提高 clog 落盘效率；该参数会减少 clog 落盘的IO，但是会在 clog 落盘前增加压缩的开销；
	3. 一般保持默认值即可；当磁盘 IO 性能较差或者磁盘容量存在限制时，考虑开启 clog 日志压缩，降低磁盘 IO 的消耗，节省空间；
5. `clog_transport_compress_all`：用于设置事务日志传输时是否压缩；
	1. 【*集群级*】【bool】；默认值为：False，取值范围为：[False, True]；动态生效；
	2. 针对网络带宽紧张，CPU 资源比较充裕的业务场景，可以打开 Clog 压缩，节省网络带宽；
6. `system_cpu_quota`：设置系统租户可以使用的 CPU 配额；虚拟租户的 CPU 配额，主要用于 clog 相关的操作，比如 clog replay；
	1. 【*集群级*】【】，取值范围为 [0, 16]，默认值为 10；动态配置项，修改后直接生效；
	2. 该参数主要影响 clog 工作线程数；如果 clog 量太大导致同步延迟较大时，可以适当调大该参数的值；追求性能的场景，则可以适当调小该参数的值；
7. `rebuild_replica_data_lag_threshold`：用于设置备副本的事务日志和主副本差距的阈值，当超过该阈值时，会触发副本重建；
	1. 【*集群级*】【容量单位】；默认值为 50GB，取值范围为：\[0, +∞)；动态生效；


##### 1.3 调优建议
为了提升 Clog 的落盘，同步的性能，在 OLTP 业务场景，我们建议采取以下优化措施：
1. 使用 Clog 聚合功能：把大概几毫秒之内的日志都聚合到一个 rpc 中发送，减少网络开销，提高并发读并控制聚合日志的落盘时间；
```sql
alter system set _clog_aggregation_buffer_amount = 4 tenant = all;
alter system set _flush_clog_aggregation_buffer_timeout = '1ms' tenant = all;
```
注意：开启 Clog 聚合需要重启 OBServer 才可生效；

2. 在 DML 并发度比较高的业务场景，增大 Clog 滑动窗口，提升事务处理，提交的性能；特别地，对与内存小的主机，建议保持默认值；
```sql
alter system set clog_max_unconfirmed_log_count = 5000 tenant = all;
```

3. 关于 Clog 压缩：
	1. 开启 Clog 压缩存储可以节省 Clog 磁盘的空间使用，但对于 Clog 落盘的效率会有影响；我们建议只对 Clog 磁盘容量有压力的系统开启 Clog 的压缩存储；
	2. 在生产系统中，我们建议开启 Clog 的压缩传输来节约网络宽带；在性能测试场景，可以关闭 Clog 的压缩传输；
```sql
alter system set enable_clog_persistence_compress = 'false' tenant = all;
alter system set clog_transport_compress_all = 5000 tenant = all;
```

在 4.0 之前的版本中，Clog 日志文件是本机所有分区混写的，副本日志落后主动拉取日志触发的源端的日志读取时随机的，存在比较严重的读放大，这可能导致通过 Clog 来同步副本的耗时比直接 Rebuild 副本的耗时要长；Clog 同步于副本 Rebiuld 的耗时取决于副本的大小以及网络和磁盘的带宽和速度；

我们可以通过调整集群配置项 rebuild_replica_data_lag_threshold 来控制触发副本 Rebiuld 的日志落后量；
1. 如果 Clog 的读放大导致 Clog 盘出现 IO 瓶颈，Clog 同步迟迟无法完成，建议调小该配置项，触发副本的 Rebiuld 操作；
	1. 建议先对分区的 Leader 副本执行转储操作，以加快副本 Rebuild 的速度；
	2. 参考后续章节关于 《负载均衡线程调优》的内容，依据网络情况合理设置负载均衡的线程数；


##### 1.4 Clog 同步状态监控
副本间的 Clog 同步状态可以通过系统虚拟表 `__all_virtual_clog_stat` 来监控；

```sql
select count(*) from __all_virtual_clog_stat where is_in_sync=0 and is_offline =0;
/*
字段解释：
	role：当前分区副本处于的角色信息；leader；follower；standby leader；standy follower；
	status：分区副本所在的状态；INIT，REPLAY，RECONFIRM，ACTIVE，TAKING_OVER，REVOKING；
	leader：分区 leader 对应的 IP 地址以及端口号；
	last_log_id：上一条已分配日志对应的 log_id；
	curr_member_list：当前成员列表；
	member_ship_log_id：成员变更日志对应的 log_id；
	is_offline：分区是否处于离线状态；
	is_in_sync：备机是否同步（备机读时间戳落后 5s 内，is_in_sync=1 表示已经同步）；
	start_id：分区滑动窗口左边界日志对应的 log_id；
	parent：级联相关的父亲节点；
	children_list：级联相关的成员列表；
	replica_type：副本类型；
	quorum：副本数；
	is_need_rebuild：是否需要 rebuild（当副本发现自己落后太多，会开启 rebuild）；
	next_replay_ts_delta：备机读时间戳和当前时间的差距；
*/
```



### 参考文档



