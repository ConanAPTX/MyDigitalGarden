---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v4.x/OceanBase 发起数据备份_V4.x/","dgPassFrontmatter":true}
---



## 1 OceanBase 发起数据备份_V4.x
### 1 发起全量数据备份
日志归档开始后，您可以发起数据备份；

#### 1.1 前提条件
在执行全量数据备份前，您需要进行以下事项：
1. 确认当前已开启归档模式，仅当日志归档任务的 `STATUS` 为 `DOING` 时，才能开始数据备份；
	1. 查看日志归档任务状态的相关操作请参见 [查看归档进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218396)，；
	2. 开启归档模式的相关操作请参见 [开启归档模式](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218401)，；

#### 1.2 备份准备（可选）
发起数据备份前，您可以为备份后的备份集设置密码；

> [!NOTE] `说明`：
> 该密码为备份后的备份集的密码。如果设置了该选项，在使用该备份集进行恢复时，需要输入该密码，且该密码不能被删除；

```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；

-- 2.（可选）执行以下命令，设置备份的密码
	obclient [(none)]> SET ENCRYPTION ON IDENTIFIED BY 'password' ONLY;
	
	-- 使用示例：
	obclient [(none)]> SET ENCRYPTION ON IDENTIFIED BY '******' ONLY;

```

> [!NOTE] `说明`：
> 设置备份密码后，在未发起数据备份前，您可以通过断开会话再重新连接数据库的方式取消本次设置；


#### 1.3 （可选）设置租户的备份并发度
1. `ha_low_thread_score`：用于设置高可用高优先级线程的当前工作线程数；*该配置项从 V4.0.0 版本开始引入*；

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





### OceanBase 配置项，变量详细介绍
查询，修改 OceanBase 配置项，变量：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 管理配置项，租户变量\|OceanBase 管理配置项，租户变量]]，；

#### 1 OceanBase 配置项
OceanBase 配项项详情：[系统配置项总览_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355400)，；

##### 1.1 集群，CPU，内存，磁盘相关设置
1. `cluster`：用于设置当前 OceanBase 集群的名称；
	1. 【*集群级*】【字符串】；默认值为：obcluster；重启生效；
	2. 该启动配置项仅建议在创建 OceanBase 集群时设置，集群生效后，请勿修改；
2. `cluster_id`：用于设置本 OceanBase 集群 ID；
	1. 【*集群级*】【整型】；默认值为：0，取值范围为：[1, 4294901759]；重启生效；
	2. 说明：该配置项仅支持在部署集群时设置，集群生效后，无法修改；
3. `rpc_timeout`：用于设置集群内部请求的超时时间；
   1. 【*集群级*】【时间类型】；默认值为：2s，取值范围为：\[0, +∞)；重启生效；
4. `/************************************ CPU 相关设置 ****** 开始 ****************************************/`
5. `cpu_count`：用于设置系统 CPU 总数。如果设置为 0，系统将自动检测 CPU 数量；
	1. 【*集群级*】【整型】；默认值为：0，取值范围为：\[0, +∞)；重启生效；
	2. 详细情况：[cpu_count V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355692)，；
6. `cpu_reserved`：用于设置系统预留的 CPU 数量，其余 CPU 将被 OceanBase 数据库独占使用；
	1. 【*集群级*】【整型】；默认值为：2，取值范围为：[0, 15]；重启生效；
7. `election_cpu_quota`：用于设置给副本选举相关的后台工作分配的 CPU 配额；
	1. 【*集群级*】【双精度浮点数】；默认值为：3，取值范围为：[0, 10]；重启生效；
8. `server_balance_cpu_mem_tolerance_percent`：在节点负载均衡策略中，用于设置 CPU 和内存资源不均衡的容忍度；
	1. 【*集群级*】【整型】；默认值为：5，取值范围为：[1, 100]；重启生效；
9. `sys_cpu_limit_trigger`：用于设置 CPU 利用率阈值，即当 CPU 利用率超过该阈值时，将暂停系统后台任务的执行；
	1. 【*集群级*】【整型】；默认值为：80，取值范围为：\[50, +∞)；重启生效；
10. `token_reserved_percentage`：在控制租户 CPU 调度中，用于设置每次预留多少比例的空闲 token 数给租户；
	1. 【*集群级*】【双精度浮点数】；默认值为：30，取值范围为：[0, 100]；重启生效；
11. `server_cpu_quota_min`：用于设置系统可以使用的最小 CPU 配额，系统会自动预留；
	1. 【*集群级*】【双精度浮点数】；默认值为：2.5，取值范围为：[0, 16]；重启生效；
12. `server_cpu_quota_max`：用于设置系统可以使用的最大 CPU 配额；
	1. 【*集群级*】【双精度浮点数】；默认值为：5，取值范围为：[0, 16]；重启生效；
13. `system_cpu_quota`：设置系统租户可以使用的 CPU 配额；
	1. 【*集群级*】【】，取值范围为 [0, 16]，默认值为 10；动态配置项，修改后直接生效；
	2. 该参数主要影响 clog 工作线程数；如果 clog 量太大导致同步延迟较大时，可以适当调大该参数的值；追求性能的场景，则可以适当调小该参数的值；
14. `workers_per_cpu_quota`：用于设置分配给每个 CPU 配额的工作线程数量；
	1. 【*集群级*】【整型】，默认值为 10，取值范围为 [2, 20]；动态生效；
	2. 租户中可同时处理的线程数由 workers_per_cpu_quota 与 cpu_quota_concurrency  2 个配置项控制，且设置时需要满足 workers_per_cpu_quota > cpu_quota_concurrency；
		1. workers_per_cpu_quota 配置项表示租户在每个 CPU 上能分配的最大工作线程数，即包括不活跃工作线程；该配置项 * 租户 max cpu = 租户能分配的最大线程数；
	3. 详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356287)，[V4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000035297)，；
15. `cpu_quota_concurrency`：用于设置租户的每个 CPU 配额所允许的最大并发数；
	1. 【*集群级*】【双精度浮点数】，取值范围为 [1, 10]，默认值为 4；动态生效；
	2. 该配置项从 V4.1.0 版本开始调整为租户级，从 V4.2.0 版本开始取值范围由 [1,10] 调整为 [1,20]；
	3. 租户中可同时处理的线程数由 workers_per_cpu_quota 与 cpu_quota_concurrency  2 个配置项控制，且设置时需要满足 workers_per_cpu_quota > cpu_quota_concurrency；
		1. cpu_quota_concurrency 配置项表示租户在每个 CPU 上活跃的工作线程数，也即并发数；该配置项 * 租户 min cpu = 租户可用的最大的工作线程数；
	4. 当物理机 CPU 足够多时，可以适当调大该参数的值；如果物理机 CPU 较少，则调大该参数的值会存在风险；
	5. 详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355693)，[V4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000035102)，；
16. `autoinc_cache_refresh_interval`：设置自增列缓存的刷新间隔；
	1. 【*集群级*】【】，取值范围为 [100ms, +∞]，默认值为 3600s；动态配置项，修改后直接生效；
	2. 当高速加载数据到无主键表时，可以考虑调大提高性能，建议设置为 43200s(12小时)；另外，该配置项不能小于无主选举的时间间隔(默认为 10 s)；
17. `weak_read_version_refresh_int`：弱一致性读版本号的刷新周期；
	1. 【*集群级*】【】，取值范围为 [0ms, +∞]，默认值为 50ms；动态配置项，修改后直接生效；
	2. 建议保持默认值，设置为 0，表示不再刷新弱一致性读版本号，不提供单调读功能；设置过大(比如超过 max_stale_time_for_weak_consistency)将会因为备副本落后过多，无法提供弱读；
18. `enable_sql_audit`：开启 Sql 审计功能；用于设置是否开启 SQL 审计功能；
	1. 【*集群级*】【bool】，取值范围为 [True, False]，默认值为 True；动态配置项，修改后直接生效；
	2. 生产环境必须开启，性能测试场景可以酌情关闭；
19. `enable_perf_event`：开启性能事件的信息收集功能；
	1. 【*集群级*】【bool】，取值范围为 [True, False]，默认值为 True；不需要重启 OBServer 生效；
	2. 生产环境必须开启，性能测试场景可以酌情关闭 enable_perf_event 和 enable_sql_audit 配置项的开启与关闭是相互依赖的，不允许：
		1. enable_perf_event 关闭状态下，将 enable_sql_audit 从关闭切换至开启；
		2. enable_sql_audit 开启状态下，将 enable_perf_event 从开启切换至关闭；
20. `enable_record_trace_log`：开启 Trace Log 功能；
	1. 【*集群级*】【bool】，取值范围为 [True, False]，默认值为 True；不需要重启 OBServer 生效；
	2. 生产环境在开启 Perf Event 和 Sql Audit 功能的情况下，此参数可以关闭；
21. `_ob_get_gts_ahead_interval`：提前获取 GTS 的时间间隔；
	1. 【*集群级*】【】，取值范围为 [0s, 1s]，默认值为 5ms；不需要重启 OBServer 生效；
	2. 1ms ~ 5ms，最佳值需要根据实现业务进行调整；如果业务 rt 较长，可以适当调大该值；
22. `/************************************ CPU 相关设置 ****** 结束 ****************************************/`
23. `/************************************ 内存相关 ****** 开始 ****************************************/`
26. `memory_limit`：表示可用的总内存大小； 	1. 【*集群级*】【容量单位】，默认值为 0，默认单位为 MB，取值范围为 [0M, +∞]；动态生效；
	2. 若该值不为 0，则系统会忽略 *memory_limit_percentage* 的设置而优先使用该参数配置的值；
	3. memory_limit=40960 与 memory_limit='40G' 设置的值相同，表示设置 OceanBase 数据库进程的使用内存上限是 40 GB；
	4. 该参数的值目前无上限。若设置的内存最大上限超过了实际的物理内存总和，则会导致系统中其他按总内存计算的值偏大；
	5. 关于设置内存使用上限：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存\|OceanBase 管理内存]]，；
27. `memory_limit_percentage`：用于设置系统总可用内存大小占总内存大小的百分比； 	1. 【*集群级*】【整型】，取值范围为 [10, 90]，默认值为 80；动态生效；
	2. 当 memory_limit 为 0 时，系统会读取该参数来计算 OBServer 的内存上线；
	3. 通常，若 OBServer 所在的机器是独占给 OceanBase 数据库，建议保持默认值，最大不能超过 90%；
28. `system_memory`：用于设置系统预留给租户 ID 为 500 的租户的内存容量； 	1. 【*集群级*】【容量单位】，取值范围为 [0M, +∞]，默认值为 30G；不需要重启 OBServer 生效；
	2. 系统预留内存不能分配给普通租户使用；
	3. 在标准的生产环境中，建议使用默认配置 30G，或根据情况分配更大的值，比如 50G；
	4. 在小规格环境，建议设置该参数的值至少为服务器内存的 5%；
29. `rootservice_memory_limit`：用于设置 RootService 的最大内存容量限制；
30. `memory_chunk_cache_size`：设置内存分配器缓存的 2M 内存块容量；
	1. 【*集群级*】【整型】，取值范围为 [0M, +∞]，默认值为 0M；不需要重启 OBServer 生效；
	2. 当值为 0 时，表示系统自适应；
31. `memstore_limit_percentage`：用于设置租户使用 MemStore 的内存占其总可用内存的百分比； 	1. 【*集群级*】【整型】；默认值为 50，即占用租户内存的50%，取值范围为：[1, 99]；动态配置项，修改后直接生效；
	2. 租户 *memstore_limit* =  *min_memory* * *memstore_limit_percentage*；前者为创建租户时指定的值；
	3. 当 MemStore 内存使用超过 *memstore_limit* *  *freeze_trigger_percentage* 时，触发冻结及后续的转储/合并等行为；
	4. 对于大规格租户，比如 32C 256G，或写多读少，可以适当调大该参数的值，用于缓存更多的更新数据，避免频繁的触发转储；
	6. 更多详细情况：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存\|OceanBase 管理内存]]，；
32. `__min_full_resource_pool_memory`：用于控制全功能型资源池的最小的内存规格；
	1. 【*集群级*】【整型】；默认值为 5368709120，取值范围为：[1073741824, +∞]；动态生效；
	2. 资源池默认为全功能型资源池，无需特别关注；
33. `leak_mod_to_check`：用于内存泄露检查，是属于内部调试的功能；
	1. 【*集群级*】【字符串】；默认值为 none；动态生效；
34. `memory_reserved`：
	1. 【*集群级*】【容量单位】；默认值为 500M，取值范围为：\[10M, +∞)；动态生效；
	2. 用于设置系统底层模块预留的内存容量大小，为了保障一些基础模块能够正常运行，一定会多预留 memory_reserved 以供使用；
35. `sql_audit_memory_limit`：用于设置 SQL 审计数据可占用的最大内存；
	1. 【*集群级*】【容量单位】；默认值为 3G，取值范围为：\[64M, +∞)；动态生效；
36. `use_large_pages`：用于管理数据库使用的内存大页；
	1. 【*集群级*】【字符串】；默认值为 false，取值范围为：[false，true，only]；重启生效；
37. `/************************************ 内存相关 ****** 结束 ****************************************/`
38. `/************************************ 磁盘空间相关 ****** 开始 ****************************************/`
39. `datafile_size`：用于设置数据文件的大小，一般不需要设置，建议保持默认值；
	1. 【*集群级*】【容量单位】；默认值为：0M，取值范围为：[0M, +∞]；动态生效；
	2. 该配置项与 datafile_disk_percentage 同时配置时，以该配置项设置的值为准。有关详细介绍，请参见 [datafile_disk_percentage](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944966)；
40. `datafile_disk_percentage`：表示占用 data_dir 所在磁盘总空间的百分比；
	1. 【*集群级*】【整型】；默认值为：90，取值范围为：[5, 99]；动态生效；
	2. 表示占用 data_dir 所在磁盘（ data_dir 所在的磁盘将被 OceanBase 数据库系统初始化用于存储数据）总空间的百分比；
	3. 该配置项与 datafile_size 同时配置时，以 datafile_size 设置的值为准；详细介绍: [datafile_size](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944967)；
41. `data_disk_usage_limit_percentage`: 用于设置数据文件最大可以写入的百分比，超过这个阈值后，禁止数据迁入；
	1. 【*集群级*】【整型】；默认值为：90，取值范围为：[50, 99]；动态生效；
42. `config_additional_dir`：用于设置本地存储配置文件的多个目录，为了冗余存储多份配置文件；
	1. 【*集群级*】【字符串列表】；默认值为：etc2，etc3；动态生效；
43. `data_dir`：用于设置存储 sstable 等数据的目录；
	1. 【*集群级*】【字符串】；默认值为：store；动态生效；
44. `enable_sql_operator_dump`：用于设置是否允许 SQL 处理过程的中间结果写入磁盘以释放内存；
	1. 【*集群级*】【布尔类型】；默认值为 True，取值范围为：True，False；动态生效；
45. `global_write_halt_residual_memory`：
	1. 【*集群级*】【整型】；默认值为：30，取值范围为：[1, 99]；动态生效；
	   1. 用于设置触发暂停普通租户写入（sys 租户不受影响）的全局剩余内存阈值。即当全局剩余内存小于这个百分比时，暂停普通租户写入(sys 租户不受影响)；
	   2. 详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355753)，；
46. `/************************************ 磁盘空间相关 ****** 结束 ****************************************/`
47. `/************************************ 压缩算法相关 ****** 开始 ****************************************/`
48. `default_compress_func`: 用于设置表数据的默认压缩算法，也可以在建表的时候指定其他压缩算法；
	1. 【*集群级*】【字符串】；默认值为：zstd_1.3.8；动态生效；详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944971)，；
49. `default_compress`: 用于设置在 Oracle 模式下，建表时使用的默认压缩策略；
	1. 【*集群级*】【字符串】；默认值为：ARCHIVE；动态生效；详细情况：[default_compress V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944970)，；
50. `default_transport_compress_func`: 用于设置整个集群的 RPC 压缩算法；
	1. 【*集群级*】【字符串】；默认值为：none；动态生效；详细情况：[default_transport_compress_func V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944973)，；
51. `default_transport_compress_func`：用于设置整个集群的 RPC 压缩算法；
	1. 【*集群级*】【字符串】；默认值为：none；动态生效；
52. `/************************************ 压缩算法相关 ****** 结束 ****************************************/`

##### 1.2 负载均衡相关配置项
1. `/************************************ 负载均衡相关 ****** 开始 ****************************************/`
2. `enable_rebalance`：用于设置是否开启自动负载均衡的功能； 	1. 【*集群级*】【布尔类型】；取值范围为：[True, False]，默认值为 True，即开启；动态生效；
	2. 从 V4.2.0 版本开始该配置项调整为【**租户级配置项**】，详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355727)，[V4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-1000000000035101)，；
	3. 系统租户下，控制是否进行租户间均衡；普通租户下，控制是否进行租户内均衡；
38. `resource_hard_limit`：用于分配 unit； 	1. 【*集群级*】【整型】；取值范围为：[1, 10000]，默认值为 100；动态生效；
	2. *resource_hard_limit* 与 *resource_soft_limit* 配置项一起配合使用；
		1. 如果 Server 的资源占用比少于 *resource_soft_limit*，则按 best_fit 来分配 Unit；
		2. 如果 Server 的资源占用比大于 *resource_soft_limit*，则按照 leaset_load 来分配 Unit；
	3. 在分配 CPU 和内存等资源时，资源总量是实际数量乘以该百分比的值。最终 Server 资源占比不能超过 resource_hard_limit；
	4. RootServer 在分配 Unit 时会根据该配置项来决定内存是否可以超卖；该配置项表示内存超卖的百分比，大于 100 表示允许超卖；
	5. 建议保持默认值，尤其是生产环境，不建议设置大于 100；
	6. *官方文档*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944388)，，；
39. `resource_soft_limit`：用于设置是否开启 Unit 均衡；【*从 V4.0.0 版本开始弃用*】；
	1. 【*集群级*】【整型】；取值范围为：[1, 10000]，默认值为 50；动态生效；
	2. 当该配置项 < 100 时， 表示 Unit 均衡开关打开；当该配置项 >= 100 时，表示 Unit 均衡开关关闭；
	3. 建议保持默认值；若希望 unit 固定运行在指定 OBServer 上，请设置该参数大于或等于100；
41. `balancer_idle_time`：用于设置负载均衡等后台任务线程空闲时的唤醒间隔时间；
	1. 【*集群级*】【时间类型】；默认值为 5m，表示 5 分钟，取值范围为：\[10s, +∞)；动态生效；
42. `balancer_log_interval`，用于负载均衡等后台任务线程打印统计日志的间隔时间；
	1. 【*集群级*】【时间类型】；默认值为 1m，表示 1 分钟，取值范围为：\[1s, +∞)；动态生效；
43. `balancer_timeout_check_interval`，用于设置检查负载均衡等后台任务超时的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 1m，表示 1 分钟，取值范围为：\[1s, +∞)；动态生效；
44. `balancer_task_timeout`，用于设置负载均衡等后台任务的超时时间；
	1. 【*集群级*】【时间类型】；默认值为 20m，表示 20 分钟，取值范围为：\[1s, +∞)；动态生效；
45. `balancer_tolerance_percentage`，；
	1. 【*集群级*】【整型】；默认值为 10，取值范围为：[1, 99]；动态生效；
	2. 用于设置负载均衡策略中，租户内多个 Unit 间磁盘不均衡程度的宽容度，如果在均值 + 或 - 宽容度范围之内，不会触发执行均衡动作；
46. `balancer_emergency_percentage`
	1. 【*集群级*】【整型】；默认值为 80，取值范围为：[1, 100]；动态生效；
	2. 用于设置 Unit 负载阈值，即当 Unit 负载超过该阈值时，允许开启副本向外迁移，即使在合并期间也执行负载均衡；
47. `server_balance_critical_disk_waterlevel`，用于设置磁盘空间的负载均衡功能的水位线；
	1. 【*集群级*】【整型】；默认值为 80，取值范围为：[1, 100]；动态生效；
	2. 当磁盘水位线超过阈值时，负载均衡策略将倾向于优先考虑磁盘均衡；
48. `server_balance_disk_tolerance_percent`，用于设置节点间磁盘负载均衡策略中不均衡程度的容忍度；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：[1, 100]；动态生效；
49. `tenant_groups`，用于设置负载均衡策略中使用的租户组；
	1. 【*集群级*】【字符串】；默认值为 空值，取值范围为：NULL；动态生效；
50. `unit_balance_resource_weight`，用于设置 Unit 均衡策略中使用的资源权重，一般不需要手工配置；
	1. 【*集群级*】【字符串】；默认值为 空值，取值范围为：NULL；动态生效；
51. `enable_sys_unit_standalone`，用于设置系统租户的 Unit 是否独占节点；
	1. 【*集群级*】【布尔类型】；默认值为 False，取值范围为：True，False；动态生效；
	2. 该配置一般采用默认值即可，当该配置项设置为 True 时，会产生的影响详细如下：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355737)，；
52. `/************************************ 负载均衡相关 ****** 结束 ****************************************/`

##### 1.3 主备库，副本相关

1. `/************************************ 副本相关 ****** 开始 ****************************************/`
2. `enable_rereplication`：用于设置是否开启自动补副本的功能；
	1. 【*集群级*】【bool】；取值范围为：[True, False]，默认值为 True；动态生效；
	2. 如果不开启该配置项，即使分区的副本数小于设置的 ob_default_replica_num，也不会进行复制；
4. `election_blacklist_interval`：用于设置主副本被废除后不允许再被选为主的时间间隔；【*从 V4.0.0 版本开始弃用*】；
	1. 【*集群级*】【时间类型】；取值范围为：[0s, 24h]，默认值为 1800s；动态生效；详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355711)，；
	2. 当 Leader 主动发生卸任后，从当前时间开始计时，在该配置项设定的时间内，该副本保证不会被改选为 Leader，即该副本加入了选举黑名单；
5. `enable_auto_leader_switch`：用于设置是否开启自动切主；【*从 V4.0.0 版本开始弃用*】；
	1. 【*集群级*】【bool】；取值范围为：[True, False]，默认值为 True；动态生效；
	2. 一般生产环境设置为默认值，轮转升级需要开启该参数；
6. `balance_blacklist_failure_threshold`，用于设置副本迁移等后台任务连续失败次数的阈值；
	1. 【*集群级*】【整型】；默认值为 5，取值范围为：[0, 1000]；动态生效；
	2. 副本迁移等后台任务连续失败次数超过该阈值后，将被放入黑名单；
7. `balance_blacklist_retry_interval`，用于设置副本迁移等后台任务被放入黑名单后的重试时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 30m，表示 30 分钟，取值范围为：[0s, 180m]；动态生效；
8. `global_index_build_single_replica_timeout`，用于建全局索引时，设置每个副本构建的超时时间；【*该配置项从 V4.0.0 版本开始删除*】
	1. 【*集群级*】【时间类型】；默认值为 48h，取值范围为：\[1h, +∞)；动态生效；
9. `ignore_replica_checksum_error`，用于设置是否忽略多副本间校验和检查发生的错误；
	1. 【*集群级*】【bool】；默认值为 False，取值范围为：[True, False]；动态生效；
10. `replica_safe_remove_time = 2h`：用于设置已删除副本可以被清理的安全保留时间；
	1. 【*集群级*】【时间类型】；默认值为 2h，取值范围为：\[1m, +∞)；动态生效；
11. `sys_bkgd_migration_change_member_list_timeout`，用于设置副本迁移时变更 Paxos 成员组操作的超时时间；
	1. 【*集群级*】【时间类型】；默认值为 1h，取值范围为：[0s, 24 h]；动态生效；
12. `sys_bkgd_migration_retry_num`，用于设置副本迁移失败时最多重试次数；
	1. 【*集群级*】【整型】；默认值为 3，取值范围为：[3, 100]；动态生效；
13. `enable_smooth_leader_switch`，表示平滑切主特性的开关；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：[True, False]；动态生效；
14. `election_blacklist_interval`，用于设置主副本被废除后不允许再被选为主的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 1800s，取值范围为：[0s, 24 h]；动态生效；
	2. OceanBase 数据库通过该配置项实现了黑名单功能。当 Leader 主动发生卸任后，从当前时间开始计时，在该配置项设置的时间内，该副本保证不会再被改选为 Leader，即该副本加入了选举黑名单；
	3. 注意：仅当主副本发生主动卸任，且 observer 进程不重启时，该副本才会加入黑名单。observer 进程重启后，黑名单状态会丢失；
15. `enable_auto_leader_switch`：用于设置是否开启自动切主；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：[True, False]；动态生效；
16. `get_leader_candidate_rpc_timeout`：用于设置自动切主策略获取切主候选者的内部请求超时时间；
	1. 【*集群级*】【时间类型】；默认值为 9s，取值范围为：[2s, 180s]；动态生效；
17. `rebuild_replica_data_lag_threshold`，用于设置备副本的事务日志和主副本差距的阈值，当超过该阈值时，会触发副本重建；
	1. 【*集群级*】【容量单位】；默认值为 50GB，取值范围为：\[0, +∞)；动态生效；
	2. 该配置项的值为 0 时，表示不触发副本重建；
18. `role_change_timeout`：用于设置主备副本切换的超时时间；
	1. 【*集群级*】【时间类型】；默认值为 10s，取值范围为：(0s, +∞)；动态生效；
19. `switchover_process_thread_count`：用于设置主备库切换相关线程池的大小；
	1. 【*集群级*】【整型】；默认值为 4，取值范围为：[1, 1000]；动态生效；
20. `/************************************ 副本相关 ****** 结束 ****************************************/`
21. `/************************************ 分区相关 ****** 开始 ****************************************/`
22. `enable_pg`：用于设置是否开启分区组功能；
	1. 【*集群级*】【布尔类型】；默认值为 False，取值范围为：[True, False]；动态生效；
23. `partition_table_check_interval`：用于设置 OBServer 从分区表中删除不存在的副本的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 30m，表示 30 分钟，取值范围为：\[1m, +∞)；动态生效；
24. `partition_table_scan_batch_count`：用于设置批量读取分区表信息的数量；
	1. 【*集群级*】【整型】；默认值为 999，取值范围为：[1, 65536]；动态生效；
25. `tablet_size`：用于设置分区内部并行处理（合并、查询等）时每个分片的大小；
	1. 【*集群级*】【容量单位】；默认值为 128M，取值范围为：\[0, +∞)；动态生效；
	2. tablet_size 的默认取值必须为 2M 的倍数，数值为 0 时，表示不采用分区内并行处理；
26. `/************************************ 分区相关 ****** 结束 ****************************************/`
27. `/************************************ RootServer 相关 ****** 开始 ****************************************/`
28. `auto_leader_switch_interval`：用于设置自动切主后台线程工作间隔时间；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【时间类型】；取值范围为：[1s, +∞]，默认值为 30s；动态生效；
29. `rootservice_async_task_queue_size`：用于设置 RootService 内部异步任务队列的大小；
	1. 【*集群级*】【整型】；默认值为 16384，取值范围为：[8, 131072]；动态生效；
30. `rootservice_async_task_thread_count`：用于设置 RootService 内部异步任务使用的线程池大小；
	1. 【*集群级*】【整型】；默认值为 4，取值范围为：[1, 10]；动态生效；
31. `rootservice_list`：用于显示 RootService 及其副本所在的机器列表；
	1. 【*集群级*】【字符串】；默认值为 空值；动态生效；
32. `rootservice_ready_check_interval`：用于设置 RootService 启动后等待和检查集群状态的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 3s，取值范围为：[100000us, 1m]；动态生效；
33. `rootservice_memory_limit`：用于设置 RootService 的最大内存容量限制；
	1. 【*集群级*】【容量单位】；默认值为 2G，取值范围为：\[2G, +∞)；动态生效；
34. `wait_leader_batch_count`：用于设置 RootService 每次发送切主命令的最大分区个数；
	1. 【*集群级*】【整型】；默认值为 1024，取值范围为：[128, 5000]；动态生效；
35. `enable_rootservice_standalone`：用于设置是否让系统租户和 RootService 独占 OBServer 节点；
	1. 【*集群级*】【bool】；默认值为 False，取值范围为：[True, False]；动态生效；
36. `lease_time`：用于设置心跳租约时长；
	1. 【*集群级*】【时间类型】；默认值为 10s，取值范围为：[1s, 5min]；动态生效；建议保持默认值；
37. `RootService` 在心跳租约时长内未收到 OBServer 的心跳信息，则认为 OBServer 为脱机状态；
38. `server_check_interval`：用于设置 Server 表一致性检查的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 30s，取值范围为：\[1s, +∞)；动态生效；
39. `server_permanent_offline_time`：
	1. 【*集群级*】【时间类型】；默认值为 3600s，取值范围为：[20s, +∞]；动态生效；
	2. 用于设置节点心跳中断的时间阈值，即节点心跳中断多久后认为其被永久下线，永久下线的节点上的数据副本需要被自动补足；
	3. 详细: [V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944411)，；
40. `ob_event_history_recycle_interval`：用于控制历史事件的回收间隔；
	1. 【*集群级*】【时间类型】；默认值为 7d，表示 7 天，取值范围为：[1d, 180d]；动态生效；建议保
41. `/************************************ RootServer 相关 ****** 结束 ****************************************/`

##### 1.4 备份恢复相关
1. `/************************************ 备份恢复相关 ****** 开始 ****************************************/`
2. `auto_delete_expired_backup`：用于设置是否开启自动删除过期备份的功能；
	1. 【*集群级*】【bool】；取值范围为：[True, False]，默认值为 False；动态生效；
	2. 在 V 2.2.77 及以上版本不再推荐使用当前配置项，建议使用 backup_dest_option 配置项中的 auto_delete_obsolete_backup 参数来设置是否开启自动删除过期备份的功能；
3. `backup_recovery_window`：用于设置备份数据可以恢复的时间；
	1. 【*集群级*】【时间类型】；取值范围为：[0, +∞]，默认值为 0，表示不自动清理；动态生效；
	2. 该配置项的取值单位为天（d），设置取值时，需带上单位才能生效；
4. `backup_dest`：用于设置基线备份和日志归档的路径；
	1. 【*集群级*】【字符串类型】；默认值为：空字符串；动态生效；
	2. 注意事项：备份进行过程中不可修改 backup_dest 配置项；
5. `backup_dest_option`：用于控制备份相关的参数；【*该配置项在 2.2.77 版本中新增*】
	1. 【*集群级*】【字符串类型】；该配置项默认值为：空字符串；动态生效；详细情况：[backup_dest_option_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944930)，；
	2. 详细情况：[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v3.x/backup_dest_option 配置项\|backup_dest_option 配置项]]，；
6. `backup_backup_dest`：用于设置备份数据备份的目的端；
	1. 【*集群级*】【字符串类型】；默认值为：空字符串；动态生效；
7. `backup_backup_dest_option`：用于控制备份备份相关的参数；*该配置项在 2.2.77 版本中新增*； 
	1. 【*集群级*】【字符串类型】；该配置项默认值为：空字符串；动态生效；详细情况：
8. `enable_log_archive`：用于设置是否开启日志归档功能；
	1. 【*集群级*】【布尔类型】；取值范围为：[True, False]，默认值为 False；动态生效；
9. `backup_log_archive_option`：用于定义备份日志的归档选项；**从 V4.0.0 版本开始弃用**；
	1. 【*集群级*】【字符串类型】；默认值为：OPTIONAL COMPRESSION=ENABLE；动态生效；详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944931)；
	2. 该配置项取值范围：
		1. MANDATORY：表示备份优先，日志归档过慢可能会导致停写；
		2. OPTIONAL：表示写入优先，写入可能会导致日志归档断流；
		3. COMPRESSION：支持 ENABLE、DISABLE、LZ4_1.0、ZSTD_1.3.8，默认为 ENABLE，使用压缩算法 LZ4_1.0；
10. `backup_zone / backup_region`
	1. `backup_zone`：用于用户指定执行备份的 Zone；
		1. 【*集群级*】【字符串类型】；该配置项默认值为：空字符串；动态生效；详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944935)，；
	2. `backup_region`：用于用户指定执行备份的 Region；
		1. 【*集群级*】【字符串类型】；默认值为：空字符串；动态生效；建议保持默认值，
	2. 不允许同时配置 backup_zone 和 backup_region，二者只能配置一个，推荐使用 backup_zone；
11. `log_archive_checkpoint_interval`：用于设置冷数据的日志归档检查点的时间间隔；【*在 V 2.2.77 版本中已存在*】【*该配置项从 V4.0.0 版本开始删除*】
	1. 【*集群级*】【时间类型】；取值范围为：[5s, 1h]，默认值为 120s；动态生效；
12. `log_archive_concurrency`：用于设置日志归档的并发度；
	1. 【*集群级*】【整型】；取值范围为：[0, +∞]，默认值为 0；动态生效；
	2. 建议保持默认值，若资源空闲可以适当提高该值；
13. `backup_concurrency`：用于设置备份时写文件系统的并发数量；
	1. 【*集群级*】【整型】；取值范围为：[0, 100]，默认值为 0；动态生效；
	2. 建议保持默认值，若资源空闲可以适当提高该值；
14. backup_net_limit：用于设置集群备份的总带宽；
	1. 【*集群级*】【容量单位】；取值范围为：[0M, +∞]，默认值为 0M，即不限制；动态生效；
15. restore_concurrency：用于设置从备份恢复租户数据时的最大并发度；
	1. 【*集群级*】【整型】；取值范围为：[0, 512]，默认值为 0；动态生效；
	2. 建议保持默认值，若资源空闲可以适当提高该值；
16. `log_archive_batch_buffer_limit = 1G`：
	1. 【*集群级*】【容量类型】；默认值为 1G，取值范围为：\[2M, +∞)；动态生效；
	2. 当物理备份开启时，log_archive_batch_buffer_limit 用于控制日志归档单机的最大内存使用量；
17. `log_restore_concurrency`：用于设置恢复日志的并发度；
	1. 【*集群级*】【整型】；默认值为 10，取值范围为：\[1, +∞)；动态生效；
18. `backup_backup_archive_log_batch_count`：用于设置备份备份日志的任务的批次数量；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：[0,1024]；动态生效；
19. `backup_backup_archivelog_retry_interval`：用于设置备份备份日志的任务的重试间隔；
	1. 【*集群级*】【时间类型】；默认值为 0s，取值范围为：[0s,1h]；动态生效；
20. `backup_backupset_batch_count`：用于设置备份备份基线的任务的批次数量；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：[0,1024]；动态生效；
21. `backup_backupset_retry_interval`：用于设置备份备份基线的任务的重试间隔；
	1. 【*集群级*】【时间类型】；默认值为 0s，取值范围为：[0s,1h]；动态生效；
22. `/************************************ 备份恢复相关 ****** 结束 ****************************************/`

##### 1.5 事务及事务日志相关
1. `/************************************ 事务及事务日志相关 ****** 开始 ****************************************/`
2. `clog_expire_days`：用于控制 Clog 文件的过期时间；*从 V4.0.0 版本开始弃用*；
	1. 当 Clog 文件最后修改时间距离当前时间超过过期时间时，该文件会被删除；
	2. 【*集群级*】【时间类型】；默认值为 3，单位为天，取值范围为：[1, 365]；动态生效；
	3. 此配置项与 clog_disk_utilization_threshold 配置项共同作用，但优先级低于后者，即当 Clog 占用磁盘水位线超过 80%，但 Clog 文件最后修改时间距离当前时间小于 clog_expire_days 配置项设置的时间，Clog 文件仍然会被删除；
4. `clog_disk_utilization_threshold`：用于控制 clog 或 ilog 磁盘空间复用的水位值；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【整型】；默认值为：80，取值范围为：[10, 100]；动态生效；
	2. 该配置项用于控制 Clog 或 ilog 磁盘的复用下限，在系统工作正常时，Clog 或 ilog 会在此水位开始复用最老的日志文件；
	3. 正常情况下，Clog 或 ilog 磁盘空间占用不会超过 80%，超过则会报 "clog disk is almost full" 的错误，提醒 DBA 处理；
5. `clog_disk_usage_limit_percentage`：用于设置事务日志可用磁盘空间的最大百分比；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【整型】；默认值为 95，取值范围为：[80, 100]；动态生效；
	2. 该配置项用于控制 Clog 或 ilog 磁盘空间的使用上限，表示允许 Clog 或 ilog 使用的磁盘空间占总磁盘空间的百分比；
	3. 该配置项是一个刚性的限制，超过此值后该 OBServer 不再允许任何新事务的写入，同时不允许接收其他 OBServer 同步的日志。对外表现是所有访问此 OBServer 的读写事务报 "transaction needs rollback" 的错误；
	4. 注意：当事务日志对应的磁盘空间的使用量达到该配置项设置的值后，磁盘将停止写入，且无法再接收日志；
6. `clog_transport_compress_all`：用于设置事务日志传输时是否压缩；
	1. 【*集群级*】【bool】；默认值为：False，取值范围为：[False, True]；动态生效；
	2. 针对网络带宽紧张，CPU 资源比较充裕的业务场景，可以打开 Clog 压缩，节省网络带宽；
7. `clog_transport_compress_func`：用于设置事务日志内部传输时使用的压缩算法；
	1. 【*集群级*】【字符串】；默认值为：lz4_1.0；动态生效；建议保持默认值；
8. `write_throttling_maximum_duration`：用于设置事务日志落盘压缩算法；
	1. 【*租户级*】【字符串】；默认值为 lz4_1.0；动态配置项，修改后直接生效；
	2. 建议保持默认值，详细情况：[v3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945170)，；
9. `clog_max_unconfirmed_log_count`：用于设置事务模块中未确认日志的最大数量；
	1. 【*租户级*】【整型】；默认值为 1500，取值范围为：[100, 50000]；动态配置项，修改后直接生效；
	2. Clog 日志滑动窗口的大小，用于设置事务模块中未确认日志的最大数量；
	3. 建议保持默认值；在 DML 并发度比较高的业务场景，增大Clog滑动窗口，提升事务处理，提交的性能；特别地，对于内存小的主机(建议设置为 5000)，提升 clog 分配效率；
10. `enable_clog_persistence_compress`：用于设置是否开启事务日志落盘压缩；*从 V4.0.0 版本开始弃用*；
	1. 【*租户级*】【字符串】；默认值为 false；动态配置项，修改后直接生效；
	2. 开启 clog 存储压缩，提高 clog 落盘效率；该参数会减少 clog 落盘的IO，但是会在 clog 落盘前增加压缩的开销；
	3. 一般保持默认值即可；当磁盘 IO 性能较差或者磁盘容量存在限制时，考虑开启 clog 日志压缩，降低磁盘 IO 的消耗，节省空间；
11. `clog_persistence_compress_func`：用于设置事务日志落盘压缩算法；
	1. 【*租户级*】【字符串】；默认值为 lz4_1.0；动态生效；
12. `trx_try_wait_lock_timeout`：用于设置语句执行过程行锁的等待时长；
	1. 【*集群级*】【时间类型】；默认值为0ms，其取值范围为：[0ms, 100ms]；动态生效；
	2. 说明: 对于 OceanBase 数据库 V3.1.0 及以上版本，该配置项将不再使用；
13. `trx_force_kill_threshold`：用于设置因冻结或切主需要杀事务时的最长等待时间；
	1. 【*集群级*】【时间类型】；默认值为 100ms，其取值范围为：[1ms, 10s]；动态生效；
14. `clog_cache_priority`：用于设置事务日志占用缓存的优先级；
	1. 【*集群级*】【整型】；默认值为 1，其取值范围为：\[1, +∞)；动态生效；
15. `clog_sync_time_warn_threshold`：用于设置事务日志同步耗时告警阈值，同步耗时超过该值产生 WARN 日志；
	1. 【*集群级*】【时间类型】；默认值为 1s，其取值范围为：\[1ms, 10000ms)；动态生效；
16. `dtl_buffer_size`：用于设置 SQL 数据传输模块使用的缓存大小；
	1. 【*集群级*】【容量单位】；默认值为 64K，其取值范围为：[4K, 2 M]；动态生效；
17. `enable_one_phase_commit`：用于设置是否开启事务提交一阶段优化的功能；
	1. 【*集群级*】【布尔类型】；默认值为 False，其取值范围为：[True, False]；动态生效；
18. `ignore_replay_checksum_error`：用于设置是否忽略回放事务日志时发生的校验和错误；
	1. 【*集群级*】【布尔类型】；默认值为 False，其取值范围为：[True, False]；动态生效；
19. `trx_2pc_retry_interval`：用于设置两阶段提交失败时候自动重试的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 100ms，其取值范围为：[1ms, 5000ms]；动态生效；
20. `index_clog_cache_priority`：用于设置事务日志索引在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，其取值范围为：\[1, +∞)；动态生效；
21. `gc_wait_archive`：用于设置分区 GC 时是否需要等待分区上所有日志归档完成；
	1. 【*集群级*】【布尔类型】；默认值为 True，其取值范围为：[True, False]；动态生效；
22. `write_throttling_maximum_duration` = 1h：
	1. 【*租户级*】【时间类型】；默认值为 1h，其取值范围为：[1s, 3d]；动态生效；
	2. 通过控制内存分配进度，控制写入速度。即指定在触发写入限速后，剩余 memstore 内存分配完所需的时间；
23. `/************************************ 事务及事务日志相关 ****** 结束 ****************************************/`

##### 1.6 转储，合并相关


1. `/************************************ 转储，合并相关设置 ****** 开始 ****************************************/`
2. `memstore_limit_percentage`：租户内的 MemStore 的内存限制；
	1. 详细介绍已有；
3. `freeze_trigger_percentage`：用于设置租户触发冻结内存的使用阈值； 	1. 【*集群级*】【整型】；默认值为70，其取值范围为：[1, 99]；动态生效；
	2. 在 V4.0.0 版本中，调整为【*租户级*】，将默认值修改为20，其取值范围为：[0, 100]；
	3. 租户 MemStore 占用内存的比例阈值，达到该值则触发 Freeze；
		1. 租户 MemStore 的大小由参数 _memstore_limit_percentage_ 决定，详情请参考：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存#4 租户内部内存管理\|OceanBase 管理内存#4 租户内部内存管理]]，；
	4. 若业务中存在批量写入操作，可以调小该参数(例如：30%)，加快转储的频次，降低单次转储的数据量，避免写入过快打爆内存；
	5. 更多详细：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存\|OceanBase 管理内存]]，；
4. `minor_compact_trigger = 2`：
	1. 【*集群级*】【整型】；该默认值为2，其取值范围为：[0, 16]；动态生效；在 V4.0.0 版本中，调整为【*租户级*】；
	2. 该配置项用于指定最大存在的 L0/L1 转储生成的 mini sstable 个数，当 partition 的转储 sstable 个数超过 minor_compact_trigger 后开始触发 L0 或者 L1 compaction；
	3. 用于控制分层转储触发 Mini SSTable 向 Minor SSTable 下压的阈值；当该层的 Mini SSTable 总数达到该配置项设定的阈值时，所有 Mini SSTable 都会被下压到下一层，组成新的 Minor SSTable；
		1. 该配置项默认值为 2，表示 Mini SSTable 数量达到 2 时，会下压到下一层组成新的 Minor SSTable；
		2. 若该配置项的值为 0 时，OB 数据库仍会进行分层转储，此时 MemTable 仍然会 Dump 为 Mini SSTable，但后续会立即调度下压合并生成新的 Minor SSTable(L1 层)，替代原有的 Minor SSTable；
	4. 详细情况请参考：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944365)，[V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001700966)；
5. `__minor_compaction_amplification_factor = 25`：控制 L0 层内部多个Mini SSTable转储的时机，默认为 25；
	1. 当所有 Mini SSTable 的总行数达到 Minor SSTable 的写放大系数比例后，才会触发 L1 层转储，否则触发 L0 层转储。当 L1 层不存在 Minor SSTable 时，所有 Mini SSTable 行数到指定阈值（由 minor_compact_trigger 控制）后才会触发 L1 层转储；
6. `minor_merge_concurrency`：用于设置小合并时的并发线程数；并行转储线程数或并行度；【*从 V4.0.0 版本开始弃用*】；
	1. 【*集群级*】，其默认值为0；其取值范围为：[0, 64]；不需要重启 OBServer 生效；
	2. 通用转储工作线程数，当设置为 0 时，会使用OB内核定义的使用10个线程作为通用转储工作线程数；
	3. 该参数一般保持默认值即可，若需要加快转储且CPU资源充足的情况下，可以适当提高通用转储的工作线程数；
7. `minor_warm_up_duration_time`：用于设置小合并产生新转储文件的预热时间；【*从 V4.0.0 版本开始弃用*】；
	1. 转储后的预热时间，目的是将待使用的新转储 SSTable 的数据提前装载到 Cache 中；
	2. 【*集群级*】，其默认值为0；其取值范围为：[0s, 64m]；不需要重启 OBServer 生效；
	3. 当 minor_warm_up_duration_time 配置项的值为 0s 时，表示不进行预热；
	4. 生产环境可以考虑设置为 0，有利于更快释放内存；
8. `writing_throttling_trigger_percentage`：用于设置写入速度的阈值；
	1. 【*租户级*】【整型】；默认值为 100，取值范围为：[1, 100]；动态生效；从 V4.0.0 版本开始默认值由 100 调整为 60；
	2. 当 MemStore 已使用的内存达到该阈值时，触发写入限速。当该配置项的值为 100 时，表示关闭写入限速机制；
9. `write_throttling_maximum_duration`：通过控制内存分配进度，控制写入速度，即指定在触发写入限速后，剩余 memstore 内存分配完所需的时间；
	1. 【*租户级*】【时间类型】；默认值为 1h，取值范围为：[1s, 3d]；动态生效；
	2. 建议保持默认值，配合 writing_throttling_trigger_percentage 使用；
10. `_ob_queuing_fast_freeze_min_count`：用于设置删除数据行时触发 queuing 表的快速冻结的阈值；
	1. 【*租户级*】【整型】；默认值为 500000，取值范围为：[10000, 100000000]；动态生效；
11. `_ob_queuing_fast_freeze_min_threshold`：用于指定与基础 SSTable 比较的最小删除计数阈值，以触发 queuing 表的快速冻结；
	1. 【*租户级*】【整型】；默认值为 10，取值范围为：[0, 100]；动态生效；
12. `_enable_parallel_minor_merge = False`：用于设置是否启用并行转储；
	1. 【*租户级*】【布尔类型】；取值范围为 [True, False]，默认值为 False；动态生效；
	2. 设置时需要指定对应的租户：alter system set \_enable_parallel_minor_merge = true tenant = 'test';
13. `/************************************ 合并，转储参数分隔 ****************************************/`
14. `enable_global_freeze_trigger`：用于设置是否开启自动触发全局冻结；自动触发全局冻结的开关；【*从 V4.0.0 版本开始弃用*】；
	1. 【*集群级*】【布尔类型】，取值范围为 [True, False]，默认值为 False；不需要重启 OBServer 生效；
	2. 如果开启，当数据内存占用超过 freeze_trigger_percentage 的值时，自动触发全局冻结和合并；
	3. **说明**：该配置项保持保持默认值即可，不推荐使用；
15. `enable_major_freeze`：用于设置是否开启自动全局冻结的功能；表示是否开启合并；
	1. 【*集群级*】【布尔类型】，取值范围为 [True, False]；不需要重启 OBServer 生效；
	2. 该参数默认值为 True，表示开启合并开关，即开启自动触发合并，若值为 False，则不能进行合并；
	3. 所有的合并的触发方法(自动触发合并、定时触发合并和手动触发合并)，均需要开启该参数；
	4. 详情请参见：[enable_major_freeze_v4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000885160)，；
16. `minor_freeze_times`：用于设置多少次小合并触发一次全局合并；【*从 V4.0.0 版本开始弃用*】； 	1. 【*集群级*】【整型】，其默认值为100，其取值范围为：[0, 65535]；动态配置项，修改后直接生效；
	2. 该配置项与 *major_compact_trigger* 配置项具有相同功能；该配置项为 0 时，表示无论转储多少次也不会触发合并；
	3. 详细情况：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 转储管理概述#2 OceanBase 数据库转储管理\|OceanBase 转储管理概述#2 OceanBase 数据库转储管理]]，；
17. `major_compact_trigger`：用于设置多少次小合并触发一次全局合并；该参数控制在转储多少次后自动转化为合并； 	1. 【*集群级*】【整型】，其默认值为 5；其取值范围为：[0, 65535]；动态生效；
	2. 在 V4.0.0 版本中，该参数调整为【*租户级*】，默认值修改成 0；
	3. 当若该配置项的取值为 0 时，表示无论转储多少次也不会触发合并；该配置项与配置项 *minor_freeze_times* 的功能相同；
	4. 该配置项用于控制 L1 层到 L2 层进行转储的时机，当 MemTable Dump Flush 的次数达到该配置项设置阈值时触发 L2 层转储；
18. `major_freeze_duty_time`：每天定时合并的时间；用于设置每日定时冻结和合并的触发时刻； 	1. 【*集群级*】【时刻】，其默认值为 02:00，取值范围为 [00:00, 23:59]；动态配置项，修改后直接生效；
	2. 在 V4.0.0 版本中，该参数调整为【*租户级*】；
19. `enable_manual_merge`：用于设置是否开启手工合并，是否开启手动合并；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【bool】，取值范围为 [True, False]，默认值为 False；动态配置项，修改后直接生效；
	2. 若参数为 True 时，alter system major freeze 并不会触发真正的合并，而需要手动对各个 Zone 进行逐个合并；
	3. 该参数用于 DBA 管理错峰是否暂停，只有在该参数为 True 时，max_merge_duration_time 才有效；若该参数值为 True，则认为 DBA 关闭自动合并；
	4. 该参数设置为开启后，表示需要手动对集群的 Zone 逐个进行合并；取值为 TRUE 表示需要手动合并；
20. `enable_merge_by_turn`：用于设置是否开启轮转合并策略；*从 V4.0.0 版本开始弃用*； 	1. 【*集群级*】，【bool】，取值范围为 [True, False]，默认值为 False；动态配置项，修改后直接生效；
	2. 若该参数设置为 True 时，即表示开启轮转合并；
21. `zone_merge_concurrency`：用于设置在合并时，支持多少个 Zone 并发；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】，【整型】，取值范围为 [0, +∞]，默认值为 1；动态配置项，修改后直接生效；
	2. 当值为 0 时，由系统根据部署情况自动选择最佳并发度；在生产环境，建议保持默认值；
22. `merge_list / zone_merge_order`：轮转合并开启时 Zone 的合并顺序；*从 V4.0.0 版本开始弃用*； 	1. 【*集群级*】，【字符串类型】，默认值为 空值；不需要重启 OBServer 生效；
	2. 用于设置 Zone 的轮转合并顺序。不指定时，由系统自动决定；生产环境建议保持默认值；
	3. OB 1.3 之前的版本中用 *\_\_all_zone* 表中的 *merge_list* 来表示，在 1.3 之后的版本中用配置项 *zone_merge_order* 来表示；
23. `zone_merge_timeout`：用于设置单个 zone 的合并超时时间；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【时间类型】；默认值为3h，取值范围为：[1s, +∞]；动态配置项，修改后直接生效；
	2. 生产环境建议保持默认值；
24. `default_progressive_merge_num`：用于设置新建一张表时默认的渐进合并次数；
	1. 【*租户级*】【整型】；默认值为 0，取值范围为：[0, +∞]；动态生效；
	2. OceanBase 数据库支持对表数据进行渐进合并，即在对表执行需要重写数据的 DDL 操作后，可以将表数据的重写分散到多次合并中，一次合并中只进行部分数据的重写；可以通过表级属性 progressive_merge_num 来控制合并行为，也可以通过该配置配置项来设置新建一张表时默认的渐进合并次数；
	3. 当表未指定 progressive_merge_num 属性时，系统会将该属性的值，设置将该配置项的值；
	4. 若值为 0，即执行渐进合并，且渐进合并的次数为 100；在生产环境一般默认即可；
	5. 若值为 1，即强制执行全量合并，不执行渐进合并；
	6. 若值为 = n，表示 X 轮合并重写完全部数据，每轮合并重写 1/X；
	7. 详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945174)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000885381)，；
25. `merger_check_interval`：每个 Zone 的合并进度检查间隔；用于设置合并状态检查线程的调度间隔；
	1. 【*集群级*】【时间类型】；默认值为10m，表示 10 分钟，取值范围为：[10s, 60m]；
26. `merge_thread_count`：用于设置每日合并工作的线程数；合并线程数；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【整型】，默认值为 0，取值范围为 [0, 256]；动态配置项，修改后直接生效；
	2. 该配置项的值为 0 时，合并的工作进程数的计算方式为 min{10, cpu_cnt * 0.3}，其中 cpu_cnt 为系统 CPU 的数量；
	3. 大部分场景建议保持默认值即可；若日常的合并经常超时，且希望加快合并的速度，可以适当调大该参数；
27. `merge_stat_sampling_ratio`：合并时统计信息采样率，当设置为 0 时则关闭统计信息的采集；
28. `max_kept_major_version_number`：存储保留最大合并版本数量；用于设置数据保留的冻结版本数量；*从 V4.0.0 版本开始弃用*；
	1. 【*集群级*】【整型】，取值范围为 [1, 16]，默认值为 2；动态配置项，修改后直接生效；
	2. 该参数默认值为 2，保留上一次合并版本数据的回查能力；如果不需要，可以改小为 1，释放空间；
29. `merger_completion_percentage`：合并好的分区数或数据大小比例；
	1. 用于设置合并完成副本数的百分比阈值，达到该阈值则认为本轮合并完成调度；
	2. 【*集群级*】【整型】，默认值为 100，取值范围为 [5, 100]；动态生效；
30. `global_major_freeze_residual_memory`：用于设置触发全局冻结的剩余内存阈值。即当剩余内存小于这个百分比时，触发全局冻结；
	1. 【*集群级*】【整型】，默认值为 40，取值范围为 [1, 99]；动态生效；详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355752)，；
	2. 说明： 该配置项取值需要比 global_write_halt_residual_memory 大，否则全局 freeze trigger 实际不生效，只能依靠 tenant 触发全局冻结；
31. `merger_switch_leader_duration_time`：用于设置每日合并时批量切主的时间间隔；
	1. 【*集群级*】【时间类型】，默认值为 3m，表示 3 分钟，取值范围为 [0s, 30]；动态生效；
32. `merger_warm_up_duration_time`：用于设置合并时，新版基线数据的预热时间；
	1. 【*集群级*】【时间类型】，默认值为 0m，取值范围为 [0s, 60m]；动态生效；
33. `minor_deferred_gc_time`：用于设置合并之后 SSTable 延迟回收的时间间隔；
	1. 【*集群级*】【时间类型】，默认值为 0s，取值范围为 [0s, 24h]；动态生效；
34. `micro_block_merge_verify_level`：用于设置合并时宏块的校验级别；
	1. 【*集群级*】【整型】，默认值为 2，取值范围为 [0，1，2，3]；动态生效；
		1. 0：表示不做校验；
		2. 1：表示验证编码算法，将读取编码后的微块以确保数据正确；
		3. 2：表示验证编码和压缩算法，除编码验证外，还将对压缩块进行解压缩以确保数据正确；
		4. 3：表示验证编码，压缩算法和丢失写保护；
35. `row_compaction_update_limit`：用于设置触发内存中行内数据合并的修改次数；
	1. 【*集群级*】【整型】，默认值为 6，取值范围为 [0，6400]；动态生效；
36. `row_purge_thread_count`：用于设置执行内存中行内数据合并的最大工作线程数；
	1. 【*集群级*】【整型】，默认值为 4，取值范围为 [1，64]；动态生效；
37. `compaction_low_thread_score`：用于设置低优先级 compaction 的工作线程占用 CPU 时间片权重；
	1. V4.0 版本新增，【租户级】【整型】；该默认值为 0，其取值范围为：[0, 100]；动态生效；
	2. 低优先级compaction(major)线程权重分数，配置项默认为 0，表示使用内部默认值 6；
38. `compaction_mid_thread_score`：用于设置中优先级 compaction 的工作线程占用 CPU 时间片权重；
	1. V4.0 版本新增，【租户级】【整型】；该默认值为 0，其取值范围为：[0, 100]；动态生效；
	2. 中优先级compaction(minor)线程权重分数，配置项默认为 0，表示使用内部默认值 6；
39. `compaction_high_thread_score`：用于设置高优先级 compaction 的工作线程占用 CPU 时间片权重；
	1. V4.0 版本新增，【租户级】【整型】；该默认值为 0，其取值范围为：[0, 100]；动态生效；
	2. 高优先级compaction(mini)线程权重分数，配置项默认为 0，表示使用内部默认值 6；
40. `/************************************ 转储，合并相关设置 ****** 结束 ****************************************/`

##### 1.7 缓存相关

1. `/************************************ 缓存相关 ****** 开始 ****************************************/`
2. `cache_wash_threshold`：用于设置触发缓存清理的容量阈值；如果内存空间小于指定值时，内存空间将被清理；
	1. 【*集群级*】【容量单位】；默认值为 4GB，取值范围为：\[0, +∞)；动态生效；
3. `plan_cache_evict_interval`：用于设置执行计划缓存的淘汰时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 1s，取值范围为：[0, +∞]；动态生效；
4. `user_block_cache_priority`：用于设置数据块缓存在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：[1, +∞]；动态生效；建议保持默认值；
5. `bf_cache_miss_count_threshold`：用于控制 bloomfilter cache 的触发次数，当宏块未命中次数达到该值时，创建 bloomfilter 缓存；
	1. 【*集群级*】【整型】；默认值为 100，取值范围为：\[0, +∞)；动态生效；值为 0 时表示关闭；
6. `bf_cache_priority`：用于设置 Bloom Filter 缓存优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：[1s, +∞]；动态生效；
7. `fuse_row_cache_priority`：用于设置融合行缓存在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：[1s, +∞]；动态生效；
8. `multiblock_read_gap_size`：用于设置一次 IO 聚合读取块缓存的最大容量；
	1. 【*集群级*】【容量单位】；默认值为 0K，取值范围为：[0k, 2M]；动态生效；
9. `user_row_cache_priority`：用于设置基线数据行缓存在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：\[1, +∞)；动态生效；
10. `user_tab_col_stat_cache_priority`：用于设置统计数据缓存在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：\[1, +∞)；动态生效；
11. `index_cache_priority`：用于设置索引在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 10，取值范围为：\[1, +∞)；动态生效；
12. `index_info_block_cache_priority`：用于设置块索引在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：\[1, +∞)；动态生效；
13. `plan_cache_evict_interval`：用于设置执行计划缓存的淘汰时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 30s，取值范围为：\[0s, +∞)；动态生效；
14. `enable_auto_refresh_location_cache`：用于设置是否开启自动刷新位置缓存信息逻辑；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：[True, False]；动态生效；
15. `auto_refresh_location_cache_rate_limit`：用于设置每个 OBServer 每秒可触发自动刷新的最大分区数目；
	1. 【*集群级*】【整型】；默认值为 1000，取值范围为：[1, 100000]；动态生效；
	2. 该配置项需要在开启自动刷新位置缓存信息逻辑功能后才能生效。有关自动刷新位置缓存信息逻辑功能的相关配置项请参见 [enable_auto_refresh_location_cache](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355715)，；
16. `auto_broadcast_location_cache_rate_limit`：用于设置每个 OBServer 每秒可广播的位置信息变化的分区的最大数量；
	1. 【*集群级*】【整型】；默认值为 1000，取值范围为：[1, 100000]；动态生效；
	2. 该配置项需要在开启自动刷新位置缓存信息逻辑功能后才能生效。有关自动刷新位置缓存信息逻辑功能的相关配置项请参见 [enable_auto_refresh_location_cache](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355715)，；
17. `user_block_cache_priority`：用于设置数据块缓存在缓存系统中的优先级；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：\[1, +∞)；动态生效；
18. `/************************************ 缓存相关 ****** 结束 ****************************************/`
19. `/************************************ Location Cache 相关 ****** 开始 ****************************************/`
20. `virtual_table_location_cache_expire_time`：用于设置虚拟表的位置信息缓存过期时间；
	1. 【*集群级*】【时间类型】；默认值为 8s，取值范围为：\[1s, +∞)；动态生效；
21. `location_cache_refresh_rpc_timeout`：表示通过 RPC 刷新 location_cache 的超时时间；
	1. 【*集群级*】【时间类型】；默认值为 500ms，取值范围为：\[1ms,+∞）；动态生效；
22. `location_cache_refresh_sql_timeout`：表示通过 SQL 刷新 location_cache 的超时时间；
	1. 【*集群级*】【时间类型】；默认值为 1s，取值范围为：\[1ms,+∞）；动态生效；
23. `force_refresh_location_cache_interval`：用于设置刷新位置缓存的最大时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 2h，表示 2 小时，取值范围为：\[1s, +∞)；动态生效；
24. `force_refresh_location_cache_threshold`：用于设置刷新位置缓存时每秒最多刷新次数的阈值，超过该阈值会被限流；
	1. 【*集群级*】【整型】；默认值为 100，取值范围为：\[1, +∞)；动态生效；
25. `location_cache_priority`：用于设置位置缓存在系统缓存服务中的优先级；
	1. 【*集群级*】【整型】；默认值为 1000，取值范围为：[1s, +∞]；动态生效；建议保持默认值；
26. `location_cache_refresh_min_interval`：用于设置位置缓存刷新请求的最小间隔，防止产生过多刷新请求造成系统压力过大；
	1. 【*集群级*】【时间类型】；默认值为 100ms，取值范围为：\[0s, +∞)；动态生效；
27. `location_cache_cpu_quota`：用于设置位置缓存模块使用的 CPU 配额；
	1. 【*集群级*】【双精度浮点数】；默认值为 5，取值范围为：[0, 10]；动态生效；
28. `location_fetch_concurrency`：用于设置单机 location_cache 刷新的最大并发数；
	1. 【*集群级*】【整型】；默认值为 20，取值范围为：[1, 1000]；动态生效；
29. `location_cache_expire_time`：用于设置分区位置缓存中分区位置信息的过期时长。不建议修改；
	1. 【*集群级*】【时间类型】；默认值为 600，取值范围为：\[1s, +∞)；动态生效；
30. `location_refresh_thread_count`：用于设置 OBServer 从 RootService 中获取分区位置信息的线程数量；
	1. 【*集群级*】【整型】；默认值为 4，取值范围为：[2, 64]；动态生效；
31. `/************************************ Location Cache 相关 ****** 结束 ****************************************/`

##### 1.8 日志相关

1. `/************************************ 日志相关 ****** 开始 ****************************************/`/_  _开始/
2. `enable_record_trace_log`：用于设置是否记录追踪日志；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：[True, False]；动态生效；
3. `enable_async_syslog`：用于设置是否启用系统日志异步写；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：[True, False]；动态生效；
4. `enable_syslog_recycle`：用于设置是否开启回收系统日志的功能；与 max_syslog_file_count 配合使用，决定回收逻辑是否考虑旧的日志文件；
	1. 仅当 max_syslog_file_count 配置项的值设置为非 0 正数时，该功能才会生效；
	2. 【*集群级*】【bool】；默认值为 False，取值范围为：[True, False]；动态生效；
	3. 生产环境建议配置为 True，同时设置 max_syslog_file_count，防止磁盘空间满；
5. `enable_syslog_wf`：用于设置是否把 WARN 以上级别的系统日志打印到一个单独的日志文件中；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：[True, False]；动态生效；
6. `enable_separate_sys_clog`：用于设置是否把系统事务日志与用户事务日志分开存储；
	1. 【*集群级*】【bool】；默认值为 False，取值范围为：[True, False]；动态生效；
7. `flush_log_at_trx_commit`：用于设置事务提交时写事务日志策略；
	1. 【*集群级*】【整型】；默认值为 1，取值范围为：[0, 2]；动态生效；
	2. *flush_log_at_trx_commit* 配置项的取值含义如下：
		1. 若取值为 0 表示不等待日志写入缓冲区；若取值为 1 表示等待日志写入磁盘；若取值为 2 表示等待日志写入缓冲区而不等落盘；
8. `max_string_print_length`：用于设置打印系统日志时，单行日志最大长度；
	1. 【*集群级*】【整型】；默认值为 500，取值范围为：\[0, +∞)；动态生效；
9. `max_syslog_file_count`：用于设置在回收日志文件之前可以容纳的日志文件数量；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：[0, +∞]；动态生效；
	2. 每个日志文件最多可以占用 256 MB 的磁盘空间。 当该配置项的值为 0 时，不会删除任何日志文件；
	3. 按照实际预留空间和日志保留策略设置；每个日志文件最多占用 256 MB的磁盘空间，需要按实际容量设置，太多容易塞满磁盘空间；
10. `syslog_level`：用于设置系统日志级别；用于设置系统日志的打印级别；
	1. 【*集群级*】【字符串】；默认值为 WDIAG；动态生效；[syslog_level V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944426)，；
11. `trace_log_sampling_interval`：用于设置定期打印跟踪日志信息的时间；
	1. 【*集群级*】【时间类型】；默认值为 10ms，取值范围为：\[0ms, +∞)；动态生效；
	2. 当 enable_record_trace_log 设置为 false 时，由 trace_log_sampling_interval 决定打印慢查询和白名单信息的时间间隔；
12. `ilog_index_expire_time`：用于设置 OBServer 可提供的 Ilog 文件读取的时间上限；
	1. 【*集群级*】【时间类型】；默认值为 60d，表示 60 天，取值范围为：\[0s,120d）；动态生效；
	2. 该配置项详细情况：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355757)，；
13. `/************************************ 日志相关 ****** 结束 ****************************************/` 

##### 1.9 读写/查询相关

1. `/************************************ 读写/查询相关 ****** 开始 ****************************************/` 
2. weak_read_version_refresh_interval：用于设置弱一致性读版本号的刷新周期，影响弱一致性读数据的延时；
	1. 【*集群级*】【时间类型】；默认值为 50ms，取值范围为：\[0ms, +∞)；动态生效；
	2. 说明：weak_read_version_refresh_interval 值为 0 时，表示不再刷新弱一致性读版本号，不提供单调读功能；
3. `large_query_worker_percentage`：用于设置预留给处理大查询的工作线程百分比；
	1. 【*集群级*】【双精度浮点数】；默认值为 30，取值范围为：[0, 100]；动态生效；
	2. TP 的场景建议保持默认值不变，或者根据实际业务应用的长短 sql 耗时和分布设置；AP的场景可以酌情提高；
4. `large_query_threshold`：用于设置查询执行时间的阈值。超过时间的请求可能被暂停，暂停后自动被判断为大查询，执行大查询调度策略；
	1. 【*集群级*】【时间类型】；默认值为 5s，取值范围为：[1ms, +∞]；动态生效；
	2. TP的场景建议保持默认值不变，AP的场景可以酌情提高；
	3. 对于存在高并发的慢 SQL 执行的联机交易系统，建议调整相关参数来限制大查询的 CPU 使用：
		1. 降低大查询线程的数量，把 large_query_worker_percentage 降为 20% 甚至 10%；
		2. 降低大查询的阈值，把 large_query_threshold 降为 2s，甚至 1s；
5. `trace_log_slow_query_watermark = 1`：
	1. 【*集群级*】【时间类型】；默认值为 1s，取值范围为：[1ms, +∞]；动态生效；建议保持默认值；
	2. 用于设置查询的执行时间阈值，如果查询的执行时间超过该阈值，则被认为是慢查询，慢查询的追踪日志会被打印到系统日志中；
6. `enable_monotonic_weak_read`：用于设置是否开启单调读；
	1. 【*租户级*】【bool】；默认值为：True，取值范围为：[True, False]；动态生效；
	2. 弱读场景下(包括备库弱读)，开启该参数保证每次弱读结果的单调一致性；否则，可以关闭以提高性能；
7. `max_stale_time_for_weak_consistency`：用于设置弱一致性读允许读到多旧的数据；
	1. 【*租户级*】【时间类型】；默认值为 5s，取值范围为：[5s, +∞]；动态生效；
	2. 默认值为 5s，表示弱一致性读最多允许读到最近 5s 内的数据；
	3. 正常保持默认值即可，若是弱读场景且业务可以接受更大的数据延迟，可以按需调整；
8. `/************************************ 读写/查询相关 ****** 结束 ****************************************/`
9. `/************************************ 用户登录相关 ****** 开始 ****************************************/`
10. `connection_control_failed_connections_threshold`：用来指定用户错误登录尝试的阀值；
	1. 【*租户级*】【整型】；默认值为 0，取值范围为：[0，2147483647]；动态生效；
	2. 将参数设置为 0 时，表示关闭该功能，即不限制用户的错误登录次数。当用户错误登录次数超过这个配置项定义的值之后，会对账户进行锁定，锁定的时长由 [connection_control_min_connection_delay](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356297) 和 [connection_control_max_connection_delay](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356298) 参数设置；
	3. 说明：当前配置项当前仅在 MySQL 模式下生效；
11. `connection_control_min_connection_delay`：指定了超过错误登录次数阈值之后，错误登录锁定的最小时长；
	1. 【*租户级*】【整型】；默认值为 1000，单位为毫秒，取值范围为：[1000，2147483647]；动态生效；
	2. 说明：当前配置项当前仅在 MySQL 模式下生效；
12. `connection_control_max_connection_delay`：指定了错误登录锁定时长的最大值，当时长达到这个最大值之后就不再增长；
	1. 【*租户级*】【整型】；默认值为 2147483647，单位为毫秒，取值范围为：[1000，2147483647]；动态生效；
	2. 说明：当前配置项当前仅在 MySQL 模式下生效；
13. `/************************************ 用户登录相关 ****** 结束 ****************************************/`

##### 1.10 并发数相关
1. `/************************************ 并发数相关 ****** 开始 ****************************************/`
2. *migrate_concurrency* / *data_copy_concurrency*  / *server_data_copy_in_concurrency* / *server_data_copy_out_concurrency*：【`从 V4.0.0 版本开始删除`】
	1. `migrate_concurrency`：用于设置内部数据迁移的最大并发数；
	2. `data_copy_concurrency`：整个集群内数据库迁移复制任务的最大并发数；用于设置系统中并发执行的数据迁移复制任务的最大并发数；
	3. `server_data_copy_in_concurrency`：单个节点上数据迁入任务的最大并发数；用于设置单个节点迁入数据的最大并发数；
	4. `server_data_copy_out_concurrency`：单个节点上数据迁出任务的最大并发数；
	5. 【从 V4.0.0 版本开始删除】，详细情况：[[15_OceanBase/02_OceanBase 基本操作/管理数据库/数据负载均衡概述\|数据负载均衡概述]]，；
3. `fast_recovery_concurrency`：用于控制 RootService 调度的快速恢复任务在 OBServer 上执行的并发数；【*从 V4.0.0 版本开始删除*】
	1. 【*集群级*】【整型】；默认值为 10，取值范围为：[1, 1000]；动态生效；
	2. 由于线程资源有限，V4.x 版本不再支持通过具体的任务类型来配置线程资源，该配置项从 V4.0.0 版本开始删除；
	3. 在 V4.x 版本中，可以使用配置项 *ha_high_thread_score* 来控制复制、Rebuild 和恢复等高可用高优先级线程的工作线程数；
4. `/************************************ 并发数相关 ****** 结束 ****************************************/`
5. `/************************************ PX 并行执行相关 ****** 开始 ****************************************/`
6. `px_workers_per_cpu_quota`：用于设置并行执行工作线程数的比例；
	1. 【*集群级*】【整型】；默认值为：10，取值范围为：[0, 20]；动态生效；
	2. 用于设置租户在每个 OB 节点上可申请的并行执行线程数量；
	3. 在 OLTP 场景，不建议大量使用并行查询，建议降低该配置项的值(例如：5)，或者将变量 parallel_servers_target 值设置一个较小的，限制总的并行线程数，以免正常的 TP 业务受到影响；
	4. 在 OLAP 场景，建议设置较大的并发线程，例如：将该配置项设置为 20；
7. `max_px_worker_count`：用于设置 SQL 并行查询引擎使用的最大线程数；
	1. 【*集群级*】【整型】；默认值为 64，取值范围为：[0, 65535]；动态生效；
	2. 建议保持默认值；
8. `px_task_size`：用于设置 SQL 并行查询引擎每个任务处理的数据量大小；
	1. 【*集群级*】【容量单位】；默认值为 2M，取值范围为：\[2M, +∞)；动态生效；
9. `/************************************ PX 并行执行相关 ****** 结束 ****************************************/`

##### 1.11 I/O，TCP 网络相关
1. `/************************************  I/O 相关 ****** 开始 ****************************************/`
2. `sys_bkgd_io_low_percentage`：用于设置系统后台 IO 最少占用的百分比；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：[0, 100]；动态生效；建议保持默认值；
	2. 当该配置项的值为 0 时，系统根据环境自动配置比例；保证不低于 128 M；
3. `sys_bkgd_io_high_percentage`：用于设置系统后台 IO 最高占用百分比；
	1. 【*集群级*】【整型】；默认值为 90，取值范围为：[1, 100]；动态生效；建议保持默认值；
4. `syslog_io_bandwidth_limit`：用于设置系统日志所能占用的磁盘 IO 带宽上限，超过带宽上限容量的系统日志将被丢弃；
	1. 【*集群级*】【容量单位】；默认值为 30MB；动态生效；建议保持默认值；
5. `user_iort_up_percentage`：用于设置用户磁盘 IO 时延阈值，即用户磁盘 IO 时延超过该阈值后，系统后台 IO 任务将被限流；
	1. 【*集群级*】【整型】；默认值为 100，取值范围为：\[0, +∞)；动态生效；
6. `disk_io_thread_count`：用于设置磁盘 IO 线程数。必须为偶数；
	1. 【*集群级*】【整型】；默认值为 8，取值范围为：[2, 32]；动态生效；
7. `multiblock_read_size`：用于设置读取数据时 IO 聚合大小；
	1. 【*集群级*】【容量单位】；默认值为 128k，取值范围为：[0K, 2M]；动态生效；
8. `net_thread_count`：接收客户端请求的网络线程数；
	1. 【*集群级*】【】，取值范围为 [0, 16]，默认值为 10；动态配置项，修改后直接生效；
	2. 该参数并不是越大越好，太高反而会增加线程切换的消耗，影响性能；根据实验室压测的结果，通常我们不建议将该参数设置为大于 24 的值；在实践中，可以 top -H 查看 MySQL IO 线程的使用效率，如果使用大于 90%，说明接收请求的线程可能成为瓶颈，建议提高该参数，如果所有线程都小于 50%，建议减小该参数，减低线程切换的开销；
9. `/************************************  I/O 相关 ****** 结束 ****************************************/`
10. `/************************************  TCP，网络相关 ****** 开始 ****************************************/`
11. `enable_tcp_keepalive = true`，用于开启或关闭客户端连接的探活机制；
	1. 【*集群级*】【布尔类型】；默认值为 true，取值范围为：[true，开启；false，关闭]；动态生效；
	2. 客户端连接的探活机制可用于检查客户端的运行状态，当客户端异常退出后，系统可以释放服务器端的资源；
12. `tcp_keepidle`，用于设置客户端连接上服务器后，如果没有数据发送，多久后会发送 Keepalive 探测分组，单位为秒；
	1. 【*集群级*】【时间类型】；默认值为 7200s，取值范围为：\[1s,+∞)；动态生效；
13. `tcp_keepintvl = 6s`，用于设置开启客户端连接的探活机制后，前后两次探测之间的时间间隔，单位为秒；
	1. 【*集群级*】【时间类型】；默认值为 6s，取值范围为：\[1s,+∞)；动态生效；
14. `tcp_keepcnt = 10`，用于设置关闭一个非活跃连接之前的最大重试次数；
	1. 【*集群级*】【整数类型】；默认值为 10，取值范围为：\[1s,+∞)；动态生效；
15. `sys_bkgd_net_percentage`：用于设置后台系统任务可占用网络带宽百分比；
	1. 【*集群级*】【整型】；默认值为 60，取值范围为：[0, 100]；动态生效；
	2. 大部分场景建议保持默认值，对于后台基线数据拷贝较慢的情况，可以适当调大；
16. `enable_ob_ratelimit`，用于在 RPC 连接中，启用区域之间的速率限制；
	1. 【*集群级*】【布尔类型】；默认值为：False，取值范围为：[true；false]；动态生效；
17. `ob_ratelimit_stat_period = 1s`，用于设置 OBServer 计算和更新最大带宽的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为：1s，取值范围为：\[100ms,+∞)；动态生效；
18. `/************************************  TCP，网络相关 ****** 结束 ****************************************/`

##### 1.12 外部存储接口相关
1. `/************************************ 网络相关 ****** 开始 ****************************************/`
2. `ob_esi_rpc_port`：用于设置 obesi 进程和 observer 进程之间的通信端口；
	1. 【*集群级*】【整型】；默认值为 2501，取值范围为：[1025, 65535]；动态生效；
3. `enable_ob_esi_process`：用于控制是否启用 obesi（外部存储接口）进程；
	1. 【*集群级*】【布尔类型】；默认值为：False，取值范围为：[true；false]；动态生效；
4. `ob_esi_session_timeout`：用于设置 obesi 进程持有活动会话资源的超时时间；
	1. 【*集群级*】【时间类型】；默认值为：1m，取值范围为：[10s, 30m]；动态生效；
5. `ob_esi_io_concurrency`：用于设置 obesi 进程的并发 I/O 数；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：\[0, +∞)；动态生效；
6. `ob_esi_memory_limit`：用于设置 obesi 进程的内存使用上限；
	1. 【*集群级*】【容量单位】；默认值为 0M，取值范围为：\[64M, +∞)；动态生效；
7. `ob_esi_syslog_level`：用于设置 obesi 进程的当前日志记录级别；
	1. 【*集群级*】【字符串】；默认值为 INFO，取值范围为：[DEBUG，TRACE，INFO，WARN，USER_ERR，ERROR]；动态生效；
8. `ob_esi_max_syslog_file_count`：用于设置 obesi 进程在回收日志文件之前可以容纳的的最大日志文件数量；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：\[0, +∞)；动态生效；
	2. 每个日志文件最多可以占用 256 MB 的磁盘空间。 当该配置项的值为 0 时，不会删除任何日志文件；
9. `/************************************ 网络相关 ****** 结束 ****************************************/`

##### 1.13 审计相关
1. `/************************************ 审计相关 ****** 开始 ****************************************/`
2. `audit_sys_operations`：用于设置是否追踪系统用户的操作行为；
	1. 【*租户级*】【bool】；默认值为 False，取值范围为：True, False；动态生效；
3. `audit_trail`：用于设置是否启用数据库审核；
	1. 【*租户级*】【字符串】；默认值为 NONE；动态生效；
	2. 详细情况：[audit_trail_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356293)，[V4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000035138)，；
4. `/************************************ 审计相关 ****** 结束 ****************************************/`

##### 1.14 其他相关
1. `/************************************ 其他 ****** 开始 ****************************************/`
2. `enable_upgrade_mode`：用于设置是否开启升级模式。在升级模式中，会暂停部分系统后台功能；
	1. 【*集群级*】【bool】；默认值为 False，取值范围为：True, False；动态生效；
3. `_ob_enable_prepared_statement`：是否开启服务器端 PS；
4. `enable_ddl`：用于设置是否允许执行 DDL；
	1. 【*集群级*】【bool】；默认值为 True，取值范围为：True, False；动态生效；建议保持默认值；
5. `ob_proxy_readonly_transaction_routing_policy`：用于控制 Proxy 对于事务的路由是否受只读语句的影响；
	1. 【*租户级*】【bool】；默认值为 True，取值范围为：True, False；动态生效；
		1. true：表示 Proxy 对于事务的路由以第一条只读语句的路由为准；
		2. false：表示 Proxy 对于事务的路由以第一条实际开启事务的语句为准；
	2. 建议将该配置项修改为 False，表示 OBProxy 对于事务的路由以第一条实际开启事务的语句为准；
6. `enable_sql_extension`：用于设置是否开启租户的 SQL 扩展功能；该配置项当前仅在 MySQL 模式下生效；
	1. 【*租户级*】【bool】；默认值为 False，取值范围为：True, False；动态生效；建议保持默认值；
7. `workarea_size_policy`：用于设置手动或者自动调整 SQL 工作区大小的策略；
	1. 【*租户级*】【字符串】；默认值为 AUTO，取值范围为：MANUAL, AUTO；动态生效；建议保持默认值；
		1. MANUAL，表示手动调整；AUTO，表示自动调整；
8. `open_cursors`：用于限单个 Session 可以同时打开的游标数量；
	1. 【*租户级*】【整型】；默认值为 50，取值范围为：[0, 65535]；动态生效；
	2. 一般保持默认值即可；若应用回话需要打开更多 cursor，或者遇到报错：maximum open cursors exceeded，则需要调高该参数的值；
9. `ob_enable_batched_multi_statement`：用于设置是否启用批处理功能的成组执行优化；
	1. 【*租户级*】【bool】；默认值为 False，取值范围为：True, False；动态生效；
	2. 一般保持默认值即可，若在 JDBC 通过文本协议 batch 执行多条 update 语句时，考虑开启该参数以提高吞吐量；
10. `stack_size`：用于设置程序函数调用栈的大小。配置时需说明单位，默认单位为 MB；
	1. 【*集群级*】【容量单位】；默认值为 512K，取值范围为：[512K, 20M]；重启服务生效；
	2. 从 V4.0.0 版本开始默认值由 1M 调整为 512K；
11. `debug_sync_timeout`：用于打开 debug sync 调试开关，并设置其超时时间；
	1. 【*集群级*】【时间类型】；默认值为 0，取值范围为：\[0, +∞)；动态生效；
12. `debug_sync_timeout` 的值为 0 时，表示关闭 debug sync 调试；
13. `enable_rich_error_msg`：用于设置是否在客户端消息中添加服务器地址、时间、追踪 ID 等调试信息；
	1. 【*集群级*】【布尔类型】；默认值为 False，取值范围为：[True, False]；动态生效；
		1. True：表示会在错误消息前添加 OBServer 的 ip:port、出错时间和日志 Trace ID；
		2. False：表示保持文本错误消息内容不变；
14. `builtin_db_data_verify_cycle`：用于设置数据坏块自检周期，单位为天。当值为 0 时表示不检查；
	1. 【*集群级*】【整型】；默认值为 20，取值范围为：[0, 360]；动态生效；
15. `data_storage_warning_tolerance_time`：用于控制数据盘状态设为 WARNING 状态的容忍时间；
	1. 【*集群级*】【时间类型】；默认值为 30s，取值范围为：[10s, 300s]；动态生效；
	2. 从 V4.0.0 版本开始默认值由 30s 调整为 5s，取值范围由 [10s，300s] 调整为 [1s, 300s]；
	3. 当出现非预期的 IO 失败时，OceanBase 数据库的 IO Manager 会通过探测线程尝试探测数据盘状态，如果超过该配置项指定的时间，探测发现这台机器的数据盘状态仍然未恢复正常，探测线程会将该数据盘状态设置为 WARNING，该状态会触发切主等事件以正常服务业务请求；
	4. 详细情况：[[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.18 其他/创建索引直至超时失败\|创建索引直至超时失败]]，；
16. `data_storage_error_tolerance_time` ：用于控制数据盘状态设为 `ERROR` 状态的容忍时间；
	1. 【*集群级*】【时间类型】；默认值为 300s，取值范围为：[10s, 7200s]；动态生效；
	2. 当出现非预期的 IO 失败时，OceanBase 数据库的 IO Manager 会通过探测线程尝试探测数据盘状态，如果超过该配置项指定的时间，探测发现这台机器的数据盘状态仍然未恢复正常，探测线程会将该数据盘状态设置为 `ERROR`，该状态会触发停机等事件以避免向该节点发送的请求失败；
	3. 详细情况：[[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.18 其他/创建索引直至超时失败\|创建索引直至超时失败]]，；
17. `dead_socket_detection_timeout`：用于设置检测失效 Socket 的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 10s，取值范围为：[0s, 2h]；动态生效；
	2. 当该配置项的值为 0 时，表示禁用 Socket 级故障检测；
	3. 当该配置项的值大于 0 时，表示启用 Socket 级故障检测，且当发送端有数据超过 设置的时间还未收到对方的应答，则认为当前 Socket 不可用；
18. `internal_sql_execute_timeout`：用于设置系统内部 SQL 请求的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 30s，取值范围为：[1000us, 1h]；动态生效；
	2. 从 V3.2.3 BP7 版本开始取值范围由 [1000us, 10min] 调整为 [1000us, 1h]；
19. `migration_disable_time`：用于设置因磁盘满等原因导致某个节点数据迁入失败时的暂停迁入时长；
	1. 【*集群级*】【时间类型】；默认值为 3600s，取值范围为：\[1s, +∞)；动态生效；
20. `schema_history_expire_time`：可用于设置需要保留的完整元数据历史数据的范围，也可用于设置延迟删除的租户的保留时间；
	1. 【*集群级*】【时间类型】；默认值为 7d，d 表示天，取值范围为：[1h, 30d]；动态生效；
21. `enable_record_trace_id`：用于设置是否记录应用端设置的追踪 ID；
	1. 【*集群级*】【布尔类型】；默认值为 True，取值范围为：[True, False]；动态生效；
22. `enable_upgrade_mode`：用于设置是否开启升级模式。在升级模式中，会暂停部分系统后台功能；
	1. 【*集群级*】【布尔类型】；默认值为 False，取值范围为：[True, False]；动态生效；
	2. 在升级前，对于需要禁止的功能（例如， DDL 操作），您可以统一通过该系统配置项来控制，使升级流程更简单。升级前将 enable_upgrade_mode 设置为 True，升级后将其设置为 False；
23. `high_priority_net_thread_count`：用于设置高优先级网络线程数；
	1. 【*集群级*】【整型】；默认值为 0，取值范围为：[0, 64]；重启生效；
	2. 该配置项的取值为 0 时，表示关闭该功能；
24. `mysql_port`：用于设置 SQL 服务协议端口号；
	1. 【*集群级*】【整型】；默认值为 2880，取值范围为：[1025, 65535]；动态生效；
25. `obconfig_url`：用于设置 OBConfig 服务的 URL 地址；该参数的值即为 *Configurl*；
	1. 【*集群级*】【字符串类型】；默认值为 空值；动态生效；
26. `rpc_port`：用于设置远程访问的协议端口号；
	1. 【*集群级*】【整型】；默认值为 2500，取值范围为：[1025, 65535]；动态生效；
27. `ssl_client_authentication`：用于设置是否开启 SSL 连接功能；
	1. 【*集群级*】【布尔类型】；默认值为 False，取值范围为：[True, 不开启；False，开启]；动态生效；
	2. 注意：ssl_client_authentication 配置后，仅在服务器重新启动所有 ca、cert、key 等文件后才生效；
28. `tenant_task_queue_size`：用于设置每个租户的请求队列大小；
	1. 【*集群级*】【整型】；默认值为 65536，取值范围为：\[1024, +∞)；动态生效；
29. `zone`：用于设置节点所在的 Zone 的名字。该配置项一般不需要配置；
	1. 【*集群级*】【字符串类型】；默认值为 空值；动态生效；
30. `recyclebin_object_expire_time`：用于自动 Purge 回收站中过期的 Schema对象；
	1. 【*集群级*】【时间类型】；默认值为 0s，取值范围为：\[0s, +∞)；动态生效；
	2. 当其值为 0s 时，表示关闭自动 Purge 回收站功能；
	3. 当其值为非 0s 时，表示回收一段时间前进入回收站的 Schema 对象；
31. `default_row_format`：用于设置在 MySQL 模式下，建表时使用的默认行格式；
	1. 【*集群级*】【字符串】；默认值为 DYNAMIC，取值范围为：REDUNDANT，COMPAT，DYNAMIC，COMPRESSED；动态生效；
32. `enable_election_group`：用于设置是否打开选举组策略；
	1. 【*集群级*】【布尔类型】；默认值为 True，取值范围为：[True, False]；动态生效；
33. `min_observer_version`：用于设置本集群最低的 OBServer 应用程序版本号；
	1. 【*集群级*】【字符串】；默认值为 3.2.3.2；动态生效；
34. `schema_history_recycle_interval`：用于设置系统内部执行 schema 多版本记录回收任务的时间间隔；
	1. 【*集群级*】【时间类型】；默认值为 10m，取值范围为：\[0s, +∞)；动态生效；
	2. 配置项的取值为 0 时，表示关闭 schema 多版本记录回收任务功能；非 0 表示执行 schema 多版本记录回收任务的时间间隔；
35. `optimizer_index_cost_adj`：用于控制优化器计算索引扫描的代价中统计信息代价的占比；
	1. 【*租户级*】【INT】；默认值为 0，取值范围为：[0，100]；动态生效；
	2. 取值为 0 时，表示优化器仅使用存储层的估行信息计算索引扫描的代价；
	3. 取值为 10 时，表示优化器计算索引扫描代价时，使用统计信息代价的 10% 加上存储层信息代价的 90% 作为索引扫描的代价；
	4. 取值为 100 时，表示优化器仅使用统计信息计算索引扫描的代价；
	5. 说明：建议使用时值设为 10，若要调整为其他取值，请咨询技术支持；详细情况：[optimizer_index_cost_adj_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-1000000000201723)，；
36. `enable_early_lock_release`：用于设置是否开启提前解行锁的功能；
	1. 【*租户级*】【布尔类型】；默认值为 False，取值范围为：[True，False]；动态生效；
37. `workarea_size_policy`：用于设置手动或者自动调整 SQL 工作区大小的策略；
	1. 【*租户级*】【字符串类型】；默认值为 AUTO，取值范围为：[MANUAL，表示手动调整；AUTO，表示自动调整]；动态生效；
38. `ob_ssl_invited_common_names`：用于记录当前租户运行的应用身份列表，该身份来自 SSL 双向认证中 Client 端证书 subject 中的 cn 字段（common name）；
	1. 【*租户级*】【字符串】；默认值为 NONE；动态生效；
	2. 详细情况：[ob_ssl_invited_common_names_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356309)，；
39. `job_queue_processes`：用来设定每个租户上可以同时运行的任务数上限，目的是为了控制任务挤占租户的资源；
	1. 【*租户级*】【整型】；默认值为 1000，取值范围：[0,1000]；动态生效；
	2. 设置 job_queue_processes 为 0，则表示定时任务不启动；
	3. **说明**：该配置项当前仅在 Oracle 模式下生效；
40. `/************************************ 其他 ****** 结束 ****************************************/` 

#### 2 OceanBase 系统变量

##### 2.1 内存，日志，目录路径

1. `/************************************ 内存 ****** 开始 ****************************************/` 
2. `ob_sql_work_area_percentage`：用于 SQL 执行的租户内存百分比限制；
	1. 【*GLOBAL*】【int】；默认值为 5，取值范围为: [0，100]；
	2. 该变量是 SQL 工作区内存参数，表示 SQL 可能耗费大量内存的 SQL 排序等阻塞性算子所使用的内存；
	3. 一般保持默认值即可；租户内存大小时可以适当调大(比如调大到20)，只读或AP 场景也可适当调大；
3. `ob_sql_audit_percentage`：用于设置 SQL Audit 内存上限占当前租户内存的百分比；
	1. 【*GLOBAL*】【int】；默认值为 3，取值范围为: [0，80]；
4. `/************************************ 内存 ****** 结束 ****************************************/`
5. `/************************************ 日志 ****** 开始 ****************************************/`
6. `binlog_row_image`：用于控制是否记录全列日志；

   1. 【*GLOBAL，SESSION*】【enum】；默认值为 FULL，取值范围为: [FULL，MINIMAL，NOBLOB]；
   2. FULL，表示记录所有列；
   3. MINIMAL：表示只记录 DML 操作中修改的列，提升主从复制吞吐量，减少 binlog 大小、网络资源和服务器内存占用；
   4. NOBLOB：目前暂不支持；
   5. 建议保持默认配置，尤其对于 OMS 等数据迁移平台依赖全列日志进行日志解析；
7. `/************************************ 日志 ****** 结束 ****************************************/` 
8. `/************************************ 目录路径 ****** 开始 ****************************************/`
9. `datadir`：用于查看数据存储的本地磁盘路径；
	1. 【*GLOBAL*】【varchar】；默认值为 /usr/local/mysql/data/，取值范围为: [N/A]；
	2. 该变量仅用于兼容，OceanBase 数据库当前暂未实现该变量的功能；
10. `plugin_dir`：用于查看存放插件 DLL 的路径；
	1. 【*GLOBAL*】【varchar】；默认值为 ./plugin_dir/，取值范围为: [N/A]；
2. `secure_file_priv`：控制导入或导出到文件时可以访问的路径。仅 DBA 可以设置该变量，其他人无法设置；
	 1. 【*GLOBAL*】【varchar】；默认值为 NULL；根据需要设置，一般保持默认值；
	 2. 详细情况：[secure_file_priv_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944616)，；
3. `/************************************ 目录路径 ****** 结束 ****************************************/`


##### 2.2 自增列，外键约束
1. `/************************************ 自增列 ****** 开始 ****************************************/`
2. `auto_increment_increment`：用于设置自增步长，仅用于 MySQL 客户端登录；
	1. 【*GLOBAL，SESSION*】【uint】；默认值为 1，取值范围为：[1, 65535]；
3. `auto_increment_offset`：用于确定 AUTO_INCREMENT 列值的起点；
	1. 【*GLOBAL，SESSION*】【uint】；默认值为 1，取值范围为：[1, 65535]；
4. `last_insert_id / identity`：用于返回本 Session 最后一次插入的自增字段值；
	1. 【*SESSION*】【uint】；默认值为 0，取值范围为：[0, 18446744073709551615]；
	2. 两个系统变量是同义词，可以通过 select @@identity  查询； 
5. `sql_auto_is_null`：该系统变量会被一些特殊的驱动程序，例如 ODBC 使用，用于控制是否获取最后插入行的自增列值；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 OFF，取值范围为：[OFF, ON]；
	2. 详细情况：[sql_auto_is_null_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944617)，；
6. `auto_increment_cache_size`：用于设置缓存的自增值个数；
	1. 【*GLOBAL*】【int】；默认值为 1000000，取值范围为：[1, 100000000]；
7. `div_precision_increment`：用于设置除法结果精度在被除数精度基础上的增量，是 MySQL 兼容功能；
	1. 【Oracle，MySql】【*GLOBAL，SESSION*】【int】；默认值为 4，取值范围为：[0, 30]；
8. `/************************************ 自增列 ****** 结束 ****************************************/`
9. `/************************************ 外键约束 ****** 开始 ****************************************/`
10. `foreign_key_checks`：用于设置是否在 DML 中检查外建约束；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 1，取值范围为: 0，不检查外键约束；1，检查外键约束；
	2. 该变量设置后，仅对 DML 操作有效，DDL 操作不受影响；
	3. 绝大多数场景保持默认值即可；OMS 数据迁移场景根据提示信息关闭；
11. `/************************************ 外键约束 ****** 结束 ****************************************/`


##### 2.3 回收站，事务，审计
1. `/************************************ 回收站 ****** 开始 ****************************************/`
2. `recyclebin`：用于设置是否开启回收站功能；根据需要设置，一般保持默认值；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 OFF，取值范围为: [0(OFF)，1(ON)]；
	2. 启用回收站后，删除的表及其依赖对象将放置在回收站中；当禁用回收站时，删除的表及其依赖对象不会放置在回收站中，会直接删除；
	3. ON，1，表示启用，启用回收站后，删除的表及其依赖对象将放置在回收站中；
	4. 该变量默认值为 OFF，0，表示不启用，当禁用回收站时，删除的表及其依赖对象不会放置在回收站中，会直接删除；
3. `/************************************ 回收站 ****** 结束 ****************************************/`
4. `/************************************ 事务 ****** 开始 ****************************************/`
5. `autocommit`：用于设置是否自动提交事务；根据需要设置，一般保持默认值；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 1，取值范围为: [0，1]；
	2. 系统变量值为 1，表示自动提交；系统变量值为 0，表示不自动提交；
6. `transaction_isolation / tx_isolation`：用于设置事务隔离级别；这2个变量是联动的，修改一个另外一个也会自动跟着变化；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 READ-COMMITTED；
	2. 该参数默认值为：READ-COMMITTED，及表示默认的事务隔离级别为【读已提交(Read Committed)】；
	3. 详细情况：[MySql 模式_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354746)，[Oracle 模式_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355384)，；
7. `ob_proxy_set_trx_executed`：用于设置用户是否通过 OBProxy 发送 START TRANSACTION Syntax 给 OBServer；
	1. 【*SESSION*】【bool】；默认值为 0，取值范围为: [0，不发送；1，发送]；
	2. 该变量支持在创建租户时指定，租户创建后不能通过 SQL 语句修改；
8. ob_trx_timeout / ob_trx_idle_timeout： 	1. `ob_trx_timeout`：用于设置事务超时（未提交）时间，单位为微秒；【推荐使用默认值】
		1. 【*GLOBAL，SESSION*】【int】；默认值为 100000000，即 100s，取值范围为: [NULL]；
		2. 从 V4.0.0 版本开始默认值由 100000000 调整为 86400000000，即 86400s，1天；
		3. 事务执行时间超过此值会给应用返回事务超时的错误，错误码为 -6210，此时需要应用发起 ROLLBACK 语句回滚该事务；
	2. `ob_trx_idle_timeout`：用于设置事务空闲超时时间，即事务中两条语句之间的执行间隔超过该值时超时，单位为微秒；
		1. 【*GLOBAL，SESSION*】【int】；默认值为 120000000，即120s，取值范围为: [100000000，+∞]；
		2. 从 V4.0.0 版本开始默认值由 120000000 调整为 86400000000，即 86400s，1天；
		3. 表示 Session上一个事务处于的 IDLE 状态的最长时间，即长时间没有 DML 语句或结束该事务，则超过该时间值后，事务会自动回滚，再执行 DML 语句会给应用返回错误码 -6224，应用需要发起 ROLLBACK 语句清理 Session 状态；
		4. 根据需要设置，一般保持默认值；
9. `transaction_read_only / tx_read_only`：用于控制是否仅允许开启只读事务；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 False，取值范围为: True，只读事务；False，不是只读事务；
	2. 当 transaction_read_only 设置为 True 时，不允许执行 DML 语句；
	3. 设置后，仅影响该用户，不影响其他用户；这 2 个变量是联动的，修改一个另一个也会自动变化；
10. `ob_trx_lock_timeout`：用于设置事务的等锁超时时长，单位为微秒；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 -1，取值范围为: [0，+∞]；
	2. 一般默认值即可，若希望控制事务持锁时间，减少锁等待，可以按需调整；
11. `/************************************ 事务 ****** 结束 ****************************************/`
12. `/************************************ 审计 ****** 开始 ****************************************/`
13. `ob_enable_sql_audit`：用于控制当前租户是否开启 SQL Audit 功能；
	1. 【*GLOBAL*】【bool】；默认值为 true，取值范围为: true，表示开启；false，表示关闭；
	2. 当 enable_sql_audit 为 false 时，全部的租户均不会开启 sql audit 功能；
14. `ob_enable_trace_log`：用于设置是否使用 trace 日志；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 0，取值范围为：0，不使用；1，使用；
15. `ob_enable_show_trace`：用于设置是否使用 show trace 日志；
	1. 在 v4.0.0 版本中被引入，【*GLOBAL，SESSION*】【bool】默认值为 0，取值范围为：0，不开启；1，开启；
	2. show trace 功能在 4.0.0 版本暂不支持，4.1 版本支持；该变量从 V4.1.0 开始生效范围调整为 Session；
16. `/************************************ 审计 ****** 结束 ****************************************/`

##### 2.4 大小写，字符集和字符串，最大值

1. `/************************************ 大小写 ****** 开始 ****************************************/` 
2. `lower_case_table_names`：用于设置是否对大小写敏感；一般保持默认值即可；
	1. 【*GLOBAL*】【int】；默认值为 1，取值范围为: [0，1，2]；
		1. 0：表名将按照指定的大小写形式进行存储，并以区分大小写形式进行比较；
		2. 1：表名将按照小写形式进行存储，并以不区分大小写形式进行比较；
		3. 2：表名将按照指定的大小写形式进行存储，并以不区分大小写形式进行比较；
	   1. 该变量仅支持使用 CREATE TENANT 语句创建租户时指定，租户创建后不能通过 SQL 语句修改；
	   2. 详细介绍：[lower_case_table_names_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946300)；
3. `/************************************ 大小写 ****** 结束 ****************************************/`
4. `/************************************ 字符集和字符串 ****** 开始 ****************************************/` 
5. `character_set_server，collation_server`：系统变量指定服务器的字符集和字符序；
	1. *character_set_server*：用于设置服务器字符集；根据需要设置，一般保持默认值；
		1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4；详细情况：[character_set_server_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946280)，；
	1. *collation_server*：用于设置服务器默认字符集和字符序；根据需要设置，一般保持默认值；
		1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4_general_ci；详细情况：[collation_server_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946284)，；
6. `character_set_database，collation_database`：系统变量指定数据库的默认字符集和字符序；
	1. *character_set_database*：用于设置默认数据库的字符集；根据需要设置，一般保持默认值；
		1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4；在 MySql 租户 V3.2.4 版本该变量当前暂不生效；
		2. 详细情况：[MySql_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944914)，[Oracle_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946277)，；
	2. *collation_database*：设置创建数据库默认字符集和字符序；根据需要设置，一般保持默认值；
		1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4_general_ci；详细情况：[collation_database_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946283)，；
7. `character_set_client`：用于设置客户端发送语句的字符集；根据需要设置，一般保持默认值；
	1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4，取值范围为: [utf8mb4，binary，gbk]；
8. `character_set_connection`：系统变量用于设置收到语句后应转换的字符集；根据需要设置，一般保持默认值；
	1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4，取值范围为: [utf8mb4，binary，gbk]；
	2. 服务器将客户端发送的语句从 character_set_client 转换为 character_set_connection；
9. `character_set_results`：用于设置服务器在将结果集或错误消息发送回客户端之前应转换为的字符集；一般保持默认值；
	1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4，取值范围为: [utf8mb4，binary，gbk]；
10. `collation_connection`：用于设置连接使用的字符集和字符序；根据需要设置，一般保持默认值；
	1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4_general_ci；详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944919)，；
11. `character_set_system`：用于查看服务器使用的字符集；
	1. 【*GLOBAL，SESSION*】【string】；默认值为 utf8mb4，取值范围为: [N/A]；
12. `character_set_filesystem`：用于设置文件系统字符集类型；
	1. 【*GLOBAL，SESSION*】【string】；默认值为 BINARY；
13. `sql_warnings`：用于在出现告警时控制单行 INSERT 语句是否生成信息字符串；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 OFF，取值范围为: [ON，OFF]；
14. ON：生成一个信息字符串；OFF：不生成信息字符串；
15. `init_connect`：用于设置服务器为每个连接的客户端执行的字符串，该字符串由一条或多条 SQL 语句组成，用分号分隔；
	1. 【*GLOBAL*】【varchar】；默认值为 N/A，取值范围为: [N/A]；
16. `nls_sort`：表示字符串值的排序规则；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为BINARY，取值范围为: [BINARY]；
17. `nls_comp`：表示字符串值的比较规则
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为BINARY，取值范围为: [BINARY]；
18. `nls_characterset`：用于查看数据库中 CHAR、VARCHAR2、CLOB 等数据类型的默认字符集；
	1. 【*GLOBAL*】【varchar】；默认值为 AL32UTF8，取值范围为: [N/A]；
19. `nls_nchar_characterset`：表示数据库默认字符集，用于 NCHAR、NVARCHAR2、NCLOB 等数据类型；
	1. 【*GLOBAL*】【varchar】；默认值为 AL16UTF16，取值范围为: [AL16UTF16]；
20. `/************************************ 字符集和字符串 ****** 结束 ****************************************/`
21. `/************************************ 最大值 ****** 开始 ****************************************/`
22. `max_allowed_packet`：用于设置最大网络包大小，单位是 Byte；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 4194304，取值范围为: [1024, 1073741824]；
	2. Session 值仅支持查看，且 Session 值只能与 Global 值相同。使用时，客户端与 Server 端一般均需要调整；
	3. 根据需要设置，一般保持默认值，对于大数据量的更新(比如数据迁移，大查询等)，考虑按需增加配置；
23. `max_sp_recursion_depth`：用于设置可以递归调用任一指定存储过程的最大层次；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 0，取值范围为: [0, 255]；
	2. 默认值为 0 ，表示当前存储过程不可递归调用；
24. `parallel_max_servers`：用于设置每台 Server 上并行执行（Parallel eXecution，PX）线程池中的最大线程数；
	1. 【*GLOBAL*】【int】；默认值为 0，取值范围为: [0, 1800]；
	2. 该变量在 3.x 及以后版本中已不再使用。请使用 [parallel_servers_target](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000035551) 变量控制并行执行的资源使用量；
25. `parallel_servers_target`：用于设置每个 Server 上的并行查询排队条件；
	1. 【*GLOBAL*】【int】；默认值为 0，取值范围为: [0, 9223372036854775807]；
	2. 当多条并行执行（Parallel eXecution，PX）SQL 总计消耗的线程数超过并行查询排队条件后，后继并行执行 SQL 需要排队等待；
	3. 详细情况：[parallel_servers_target_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944608)，；
26. `group_concat_max_len`：用于设置允许的 GROUP_CONCAT() 函数结果的最大长度，单位为字节；【推荐使用默认值】
	1. 【*GLOBAL，SESSION*】【uint】；默认值为 1024，取值范围为: [4, 18446744073709551615]；
27. `sql_select_limit`：用于设置一个 select 查询允许返回的最大行数；【推荐使用默认值】
	1. 【*GLOBAL，SESSION*】【int】；默认值为 9223372036854775807，取值范围为: [4, 9223372036854775807]；
	2. 若一个 select 中包含了 limit 语句，那么 limit 的值比 sql_select_limit 的优先级高，默认不限制 select 返回的行数；
28. `/************************************ 最大值 ****** 结束 ****************************************/`


##### 2.5 查询，超时时间，读写模式
1. `/************************************ 查询 ****** 开始 ****************************************/`
2. `ob_query_timeout`：用于设置 SQL 最大执行时间，单位是微秒；【推荐使用默认值】 	1. 【*GLOBAL，SESSION*】【int】；默认值为 10000000，即 10s，取值范围为: [0, 3216672000000000]；
	2. 若语句执行时间超过此值会给应用返回语句超时的错误，错误码为 -6212，并回滚语句；
3. `ob_stmt_parallel_degree`：用于设置查询的并行度，即可以并行运行的任务数；【在 V4.0.0 版本中被取消】
	1. 【*GLOBAL，SESSION*】【int】；默认值为 1，取值范围为: [1, 10240]；
4. `parallel_degree_policy`：用于设置并行度选择策略；【从 V4.2.0 版本开始引入】
	1. 【*GLOBAL，SESSION*】【enum】；默认值为 MANUAL，取值范围为: [MANUAL，AUTO]；
	2. 【AUTO】：启用 Auto DOP 策略；【MANUAL】：禁用 Auto DOP 策略
5. `is_result_accurate`：当查询带有 topk 提示时，is_result_accurate 表示结果是否正确；
	1. 【*SESSION*】【bool】；默认值为 1，取值范围为: [0, 1]；
6. `net_buffer_length`：用于设置 OBServer 能接受的 SQL Query 的大小；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 16384，取值范围为: [1024, 1048576]；
	2. 该变量可通过 SET GLOBAL 语句修改 Global 生效方式下的取值，不可通过 ALTER SESSION SET 语句修改 Session 生效方式下的取值；
7. `/************************************ 查询 ****** 结束 ****************************************/`
8. `/************************************ 超时时间 ****** 开始 ****************************************/`
9. `connect_timeout`：用于设置连接超时时间，单位为秒；【推荐使用默认值】
	1. 【*GLOBAL*】【int】；默认值为 10，取值范围为: [2, 31536000]；
10. `net_read_timeout`：用于中断读数据之前等待连接的其它数据的秒数；
	1. 【*GLOBAL*】【int】；默认值为 30，取值范围为: [1, 31536000]；
	2. 当服务器从客户端读数据时，net_read_timeout 指控制何时中断的超时值；
11. `net_write_timeout`：用于设置中断写数据之前等待块写入连接的秒数；即客户端接收服务端查询返回的数据最多消耗的时间，若超过该时间则会中断连接； 	1. 【*GLOBAL，SESSION*】【int】；默认值为 60，取值范围为: [1, 31536000]；
	2. 当服务器向客户端写数据时，*net_write_timeout* 指控制何时中断的超时值；
	3. 对于通过 OBProxy 连接的集群，当该变量的生效范围设置为 Global，则需要重启 OBProxy 才会生效；
12. `ob_pl_block_timeout`：用于设置 PL 的最大超时时间，单位 us；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 3216672000000000，取值范围为: [0, 9223372036854775807]；
13. `interactive_timeout`：用于设置服务器关闭交互式连接前等待活动的秒数；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 28800，取值范围为: [1, 31536000]；
	2. 交互式客户端定义为在 mysql_real_connect() 中使用 CLIENT_INTERACTIVE 选项的客户端；
	3. 详细情况：[interactive_timeout_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944533)，；
14. `wait_timeout`：用于设置服务器关闭非交互连接之前等待活动的秒数；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 28800，取值范围为: [1, 31536000]；
	2. 详细情况：[wait_timeout_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946125)，；
15. `/************************************ 超时时间 ****** 结束 ****************************************/`
16. `/************************************ 读写模式 ****** 开始 ****************************************/`
17. `read_only`：用于设置租户是否为只读模式；
	1. 【*GLOBAL*】【bool】；默认值为 0，取值范围为: 1，租户为只读模式；0，租户为读写模式；
	2. 设置后，会影响该租户下的所有普通用户，不影响 root 权限用户；
18. `ob_read_consistency`：用于设置读一致性级别；绝大多数场景保持默认值即可；
	1. 【*GLOBAL，SESSION*】【enum】；默认值为 STRONG，取值范围为: 0 空字符串；1 FROZEN；2 WEAK；3 STRONG；
19. `ob_safe_weak_read_snapshot`：用于 Proxy 向数据库同步弱读一致性读快照；
	1. 【*SESSION*】【int】；默认值为 1，取值范围为: [0, 9223372036854775807]；
20. `/************************************ 读写模式 ****** 结束 ****************************************/`


##### 2.6 优化器，计划缓存
1. `/************************************ 优化器 ****** 开始 ****************************************/`
2. `optimizer_use_sql_plan_baselines`：用于控制优化器是否会使用 Plan Baseline 计划；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 0，取值范围为: [0，1]；
		1. 0：表示在新生成计划时，不再考虑 Plan Baseline 中的计划，直接使用优化器生成新计划并执行；
		2. 1： 表示在新生成计划时，优化器会优先使用Plan Baseline 计划，对于新的不同计划则验证通过后才使用；
3. `optimizer_capture_sql_plan_baselines`：用于控制是否自动捕获新计划到 Plan Baseline；
	 1. 【*GLOBAL，SESSION*】【bool】；默认值为 0，取值范围为: [0，1]；
	1. 0：表示不自动捕获新计划到 Plan Baseline 中；
	2. 1：表示对于新生成的计划，如果该 SQL 没有对应的 Plan Baseline，则将该计划加入到 SQL Plan Baseline， 已有 Plan Baseline 且与新计划不同， 则会触发计划演进进行验证，确定是否需要将新计划替换老的 Plan Baseline；
4. `/************************************ 优化器 ****** 结束 ****************************************/`
5. `/************************************ 计划缓存 ****** 开始 ****************************************/`
6. `ob_enable_plan_cache`：用于设置是否打开 Plan Cache；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 1，取值范围为: [0，关闭；1 打开]；
7. `ob_plan_cache_percentage`：用于设置计划缓存可使用内存占租户内存的百分比；
	1. 【*GLOBAL*】【int】；默认值为 5，取值范围为: [0，100]；
8. 计划缓存最多可使用内存（内存上限绝对值）= 租户内存上限 * ob_plan_cache_percentage/100；
9. `ob_plan_cache_evict_high_percentage`：用于设置触发计划缓存淘汰的内存大小占内存上限绝对值的百分比；
	1. 【*GLOBAL*】【int】；默认值为 90，取值范围为: [0，100]；
	2. 触发计划缓存淘汰的内存大小（淘汰计划的高水位线） = 内存上限绝对值 * ob_plan_cache_evict_high_percentage/100；
	3. 有关内存上限绝对值的相关介绍，请参见 [ob_plan_cache_percentage](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944586)，；
10. `ob_plan_cache_evict_low_percentage`：用于设置停止淘汰计划缓存的内存大小占内存上限绝对值的百分比；
	1. 【*GLOBAL*】【int】；默认值为 50，取值范围为: [0，100]；
	2. 停止淘汰计划缓存的内存大小（淘汰计划的低水位线）= 内存上限绝对值 * ob_plan_cache_evict_low_percentage/100；
	3. 有关内存上限绝对值的相关介绍，请参见 [ob_plan_cache_percentage](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944586)，；
11. `/************************************ 计划缓存 ****** 结束 ****************************************/`


##### 2.7 租户白名单，设置用户密码
1. `/************************************ 租户白名单 ****** 开始 ****************************************/`
2. `ob_tcp_invited_nodes`：用于设置租户的 IP 白名单；绝大多数场景使用 % 即可；
	1. 【*GLOBAL*】【varchar】；默认值为 127.0.0.1,::1，取值范围为: 字符串长度小于 1024；
	2. 支持百分号（%） 、短横线（_） 和 IP（多个 IP 之间用逗号分隔），支持 IP 列表匹配、掩码匹配和模糊匹配；
3. `/************************************ 租户白名单 ****** 结束 ****************************************/`
4. `/************************************ 设置用户密码 ****** 开始 ****************************************/`
5. `validate_password_check_user_name`：用于设置用户密码是否可以和用户名相同；
	1. 【*GLOBAL*】【enum】；默认值为 off，取值范围为: [on, off]；
	2. 取值为 ON 时，表示用户密码不可以和用户名相同；取值为 OFF 时，表示用户密码可以和用户名相同；
	3. 该变量需要与 validate_password_policy 变量配合使用，仅当 validate_password_policy 取值为 MEDIUM 时，该变量才会生效；
	4. 详细介绍，请参见 [validate_password_policy](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944704)，；
6. `validate_password_policy`：用于设置密码检查策略；正式生产环境建议设置成 MEDIUM；
	1. 【*GLOBAL*】【enum】；默认值为 LOW，取值范围为: [LOW, MEDIUM]；
	2. LOW：表示仅包含密码常见的检测；
	3. MEDIUM：表示包括密码长度检测、大写字母个数检测、小写字母个数检测、数字个数检测、特殊字符个数检测、用户名密码相同检测；
7. `validate_password_length`：设置用户密码最小长度；
	1. 【*GLOBAL*】【uint】；默认值为 0，取值范围为: [0,2147483647]；
8. `validate_password_mixed_case_count`：用于设置用户密码至少包含的大写字母个数以及至少包含的小写字母个数；
	1. 【*GLOBAL*】【uint】；默认值为 0，取值范围为: [0,2147483647]；
	2. 根据需要设置，测试环境可以保持默认值，正式生产环境建议调大；
9. `validate_password_number_count`：用于设置用户密码至少包含的数字个数；
	1. 【*GLOBAL*】【uint】；默认值为 0，取值范围为: [0,2147483647]；
	2. 根据需要设置，测试环境可以保持默认值，正式生产环境建议调大；
10. `validate_password_special_char_count`：用于设置用户密码至少包含的特殊字符个数；
	1. 【*GLOBAL*】【uint】；默认值为 0，取值范围为: [0,2147483647]；
	2. 根据需要设置，测试环境可以保持默认值，正式生产环境建议调大；
11. `/************************************ 设置用户密码 ****** 结束 ****************************************/`


##### 2.8 时区和日期，数据格式
1.  `/************************************ 时区和日期 ****** 开始 ****************************************/`
2. time_zone：用于设置当前租户会话使用的时区，支持 '+08:00'的 offset 形式和 Asia/Shanghai的地域形式取值；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 +8:00，取值范围为: [-12:59 ~ +13:00]；
	2. 根据需要设置，一般保持默认值；
3. `TIMESTAMP`：时间戳，单位为秒，该变量影响 select now(6)的结果；【推荐使用默认值】
	1. 【*SESSION*】【numeric】；默认值为 0，取值范围为: [0, 253402272000]；
4. `system_time_zone`：用于设置服务器机器系统时区；
	1. 该变量支持在创建租户时指定，租户创建后不能通过 SQL 语句修改；
5. `error_on_overlap_time`：用于设置夏令时重叠区时间发生歧义时是否作报错处理；【推荐使用默认值】
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 OFF，取值范围为: [ON，OFF]；
	2. ON，进行报错处理；OFF，不进行报错处理；
6. `ob_timestamp_service`：用于指定使用何种时间戳服务；【推荐使用默认值】
	1. 【*GLOBAL*】【enum】；sys 租户默认值为 LTS；普通租户默认值为 GTS；
	2. LTS：事务执行过程中使用服务器本地的时间戳做为事务的快照，不支持跨分区的访问；
	3. GTS：事务执行过程中从 GTS 服务获取全局时间戳做为事务的快照以及事务的提交版本号，使用上没有限制；
7. `nls_language`：表示数据库国际化支持中的默认语言，用于提示信息、日期、月份名以及 NLS_SORT 和NLS_DATE_LANGUAGE 的默认值；
	1. 【*SESSION*】【varchar】；默认值为 AMERICA，取值范围为: [AMERICA]；【推荐使用默认值】
8. `nls_territory`：表示数据库国际化支持中的当前地域，与 nls_language 结合使用；【推荐使用默认值】
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 AMERICA，取值范围为: [AMERICA]；
9. `nls_date_language`：表示数据库国际化支持中的日期默认语言；【推荐使用默认值】
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 AMERICA，取值范围为: [AMERICA]；
	2. 用于 TO_DATE 和 TO_CHAR 输入或返回的日期、月份、上下午、公元前后等信息；
10. `nls_calendar`：用于控制数据库使用的日历系统；【推荐使用默认值】
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 GREGORIAN，取值范围为: [GREGORIAN]；
11.  `/************************************ 时区和日期 ****** 结束 ****************************************/`
12.  `/************************************ 数据格式 ****** 开始 ****************************************/`
13. `nls_length_semantics`：表示 char、varchar2 类型 的 length 语义；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 BYTE，取值范围为: [BYTE，CHAR]；
14. `nls_nchar_conv_excp`：用于控制 NCHAR/NVARCHAR2 与 CHAR/VARCHAR2 之间转换丢失数据时是否报错；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 FALSE，取值范围为: TRUE，表示报错；FALSE：表示不报错；
15. `nls_numeric_characters`：用于控制 number 和字符串转换中字符串中小数分隔符和千分位分隔符的字符；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 空字符串，取值范围为: [NULL]；
16. `nls_currency`：用于设置数字格式元素 L 的本地货币符号；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 $，取值范围为: [N/A]；
17. `nls_iso_currency`：用于设置数字格式元素 C 的本地货币符号；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 AMERICA，取值范围为: [N/A]；
18. `nls_dual_currency`：用于设置数字格式元素 U 的本地货币符号；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 $，取值范围为: [N/A]；
19. `nls_date_format`：用于控制 date 类型转 str 的格式，以及 str 隐式转 date 的格式；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 DD-MON-RR，取值范围为: NULL；
20. `nls_timestamp_format`：用于控制 timestamp 或 timestamp ltz 类型转 str 的格式，以及 str 隐式转 timestamp 或 timestamp ltz 的格式；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 DD-MON-RR HH.MI.SSXFF AM，取值范围为: NULL；
21. `nls_timestamp_tz_format`：用于控制 timestamp tz 类型转 str 的格式，以及 str 隐式转 timestamp tz 的格式；
	1. 【*GLOBAL，SESSION*】【varchar】；默认值为 DD-MON-RR HH.MI.SSXFF AM TZR，取值范围为: NULL；
22.  `/************************************ 数据格式 ****** 结束 ****************************************/`


##### 2.9 启用/关闭功能
62. `/************************************ 启用/关闭功能 ****** 开始 ****************************************/`
63. `ob_enable_aggregation_pushdown`：用于设置是否允许聚合操作下压；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 1，取值范围为: 0，不允许；1，允许；
64. `ob_enable_hash_group_by`：用于设置是否打开 Hash Group by 的路径；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 1，取值范围为: 0，不允许；1，允许；
65. `ob_enable_blk_nestedloop_join`：用于设置是否允许打开 block nested loop join；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 0，取值范围为: 0，不允许；1，允许；
66. `ob_enable_transmission_checksum`：用于设置 Proxy 和 Server 之间是否进行数据包校验和，设置后实时生效；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 1，取值范围为: 0，不进行校验和；1，进行校验和；
	2. 一般保持默认值即可；PoC 性能压测场景可以考虑关闭该功能；
67. `ob_enable_truncate_flashback`：用于设置是否启用表截断的闪回；根据需要设置，一般保持默认值；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 0(OFF)，取值范围为: 0(OFF)，不启用；1(ON)，启用；
68. `/************************************ 启用/关闭功能 ****** 结束 ****************************************/`


##### 2.10 其他
1. `/************************************ 其他 ****** 开始 ****************************************/`
2. `explicit_defaults_for_timestamp`：用于指定 timestamp 数据类型在处理默认值和空值时是否启用非标准行为；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 ON，取值范围为: OFF，不启用；ON，启用；
3. `ob_bnl_join_cache_size`：用于设置 batch nest loop join 一次 cache 多少数据做一次 batch；
	1. 【*GLOBAL，SESSION*】【int】；默认值为 10485760，取值范围为: 1, 9223372036854775807；
4. `ob_compatibility_mode`：用于指定租户的兼容模式；
	1. 【*GLOBAL，SESSION*】【enum】；默认值为 MYSQL，取值范围为: MYSQL，兼容 MYSQL；ORACLE，兼容 ORACLE；
	2. 该变量支持在创建租户时指定，租户创建后不能通过 SQL 语句修改；
5. `ob_create_table_strict_mode`：是动态变量，用于设置建表的严格模式；
	1. 【*GLOBAL，SESSION*】【bool】；默认值为 0，取值范围为: [0，1]；
		1. 1，表示会严格按照 Locality 建立副本，任何副本建立失败，则建表失败；
		2. 0，表示全类型副本至少有 1 个，Paxos 成员组副本达到多数，则建表成功；
6. `ob_route_policy`：用于设置 OBProxy 或 Java 客户端与 OBServer 内部重试的路由策略； 	1. 【*GLOBAL，SESSION*】【enum】；默认值为 1，取值范围为: [1，2，3，4]；
	2. 取值范围为：1：READONLY_ZONE_FIRST；2：ONLY_READONLY_ZONE；3：UNMERGE_ZONE_FIRST；4：UNMERGE_FOLLOWER_FIRST
		1. 若值为：*readonly_zone_first*，为默认值，读库优先访问；
			1. 路由策略：本地常态读库 PS -> 同城常态读库 PS -> 本地合并读库 PS -> 同城合并读库 PS -> ….
			2. 优先级: zone 类型 > 合并状态 > idc 状态
		2. 若值为：*only_readonly_zone*，只访问读库；
			1. 路由策略：本地常态读库 PS -> 同城常态读库 PS -> 本地合并读库 PS -> 同城合并读库 PS -> 本地常态读库 TS -> ….
			2. 优先级: 合并状态 > idc 状态
		3. 若值为：*unmerge_zone_first*，优先访问不在合并的 zone；
			1. 路由策略：本地常态读库 PS -> 同城常态读库 PS -> 本地常态写库 PS -> 同城常态写库 PS-> …….
			2. 优先级：合并状态 > zone 类型 > idc 状态；
	3. 根据需要设置，一般保持默认值；详细情况：[ob_route_policy_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944593)，；
7. `undo_retention`：表示系统应保留的多版本数据范围，单位为秒，在转储时控制多版本数据的回收；
	1. 【*GLOBAL*】【int】；默认值为 1800，取值范围为: [0, 2]；
	2. OB 数据库转储的数据会保留多个版本的历史数据行，可以通过 undo_retention 来控制转储中保留的多版本数据范围；其中：
		1. 当 undo_retention 的值为 0 时，表示未开启多版本转储，即转储文件仅保留当前最新版本的行数据；
		2. 当 undo_retention 的值大于 0 时，表示开启多版本转储，并且转储文件保留这段时间（单位为秒）以内的多版本行数据；
	3. 租户开启多版本转储后，大版本合并会保留对应的增量转储文件，但 Major SSTable 中不会存放多版本数据；
	4. 建议该参数不要设置过大，防止因参数过大而导致保留的 SSTable 数量超限；根据需要设置，一般保持默认值；
8. `resource_manager_plan`：用于激活资源管理计划。指定不同计划，会使用不同 CPU 的额度来限定资源组下的用户活动；
	1. 【*GLOBAL*】【varchar】；默认值为 ""，取值范围为: [N/A]；不需重启服务生效，设置后 10s 内生效；
	2. 租户内的普通线程和并行执行线程会相互抢占 CPU 资源，通过为租户指定资源隔离计划，可以控制资源抢占策略，限制资源的使用量；
9. `/************************************ 其他 ****** 结束 ****************************************/`

### 参考文档：

1. 生产环境参数配置参考样例，详情情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944830)，介绍基于 TP，AP 使用场景，详细介绍集群级参数，租户级参数，系统变量；
2. [系统配置项概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355399)，[系统配置项总览](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355400)，；
3. [系统变量概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355417)，[系统变量总览](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355418)，；




</div></div>



````tab
tab: sys 租户
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2.（可选）设置租户的备份并发度
	-- 设置集群中所有租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all_user;
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all;

	-- 设置集群中指定租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = mysql_tenant;
```

> [!NOTE] `说明`：
> OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；


tab: 业务租户
```sql
-- 1. 租户管理员登录到数据库;
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.（可选）设置备份并发度；
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10;
```
````


#### 1.4 发起全量备份
完成准备工作后，您可以通过以下方式开启全量数据备份；

假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`，且租户 `mysql_tenant` 和 `oracle_tenant` 均已完成发起数据备份前的准备工作；

##### 1.4.1 系统租户发起全量备份
`sys` 租户可以对集群中的所有租户或指定租户发起全量数据备份；

（可选）设置租户的备份并发度：[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v4.x/OceanBase 发起数据备份_V4.x#1.3 （可选）设置租户的备份并发度\|OceanBase 发起数据备份_V4.x#1.3 （可选）设置租户的备份并发度]]，；

```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 3. 执行以下语句，发起全量数据备份；
	-- 3.1 对集群中的所有租户发起全量数据备份
		ALTER SYSTEM BACKUP DATABASE [PLUS ARCHIVELOG];   -- 该方式会对集群中的所有租户发起全量数据备份；
	
		-- 对集群中的所有租户发起全量数据备份的示例如下：
		ALTER SYSTEM BACKUP DATABASE;
			-- 命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 和 `oracle_tenant` 租户发起全量数据备份；
```

其中，`PLUS ARCHIVELOG` 为可选；添加 `PLUS ARCHIVELOG` 后，可以在数据备份过程中同时将归档日志一起备份，最终备份目录下会生成一份带有归档日志的完整数据集。该数据集具备恢复能力，您可以不依赖归档日志，使用该数据集将租户的数据恢复到 `min_restore_scn` 位点；

此外，对于社区版用户和单机版用户，可以通过该数据集创建备租户，有关如何使用带归档日志的完整数据集创建备租户的详细操作，请参见 [使用 BACKUP DATABASE PLUS ARCHIVELOG 功能创建备租户](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218767)，；

```sql
	-- 3.2 对集群中的指定租户发起全量数据备份
		-- 该方式仅对指定租户发起全量数据备份，不会影响集群中的其他租户。语句如下：
		ALTER SYSTEM BACKUP TENANT [=] mysql_tenant [PLUS ARCHIVELOG];
	
		-- 对 `mysql_tenant` 租户发起全量数据备份的示例如下：
		obclient [(none)]> ALTER SYSTEM BACKUP TENANT = mysql_tenant; 
```


> [!NOTE] `说明`：
> 同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；

命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 租户发起全量数据备份；
全量备份过程中，可以实时查看全量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


##### 1.4.2 用户租户发起全量备份
用户租户可以对本租户发起全量数据备份，不会影响其他租户；

```sql
-- 1. 租户管理员登录到数据库;
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 3. 执行以下语句，发起全量数据备份
	ALTER SYSTEM BACKUP DATABASE [PLUS ARCHIVELOG];

	-- 用户租户对本租户发起全量数据备份的示例如下：
	obclient [(none)]> ALTER SYSTEM BACKUP DATABASE;
```
其中，`PLUS ARCHIVELOG` 为可选。添加 `PLUS ARCHIVELOG` 后，可以在数据备份过程中同时将归档日志一起备份，最终备份目录下会生成一份带有归档日志的完整数据集。该数据集具备恢复能力，您可以不依赖归档日志，使用该数据集将租户的数据恢复到 `min_restore_scn` 位点；

此外，对于社区版用户和单机版用户，可以通过该数据集创建备租户，有关如何使用带归档日志的完整数据集创建备租户的详细操作，请参见 [使用 BACKUP DATABASE PLUS ARCHIVELOG 功能创建备租户](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218767)，；

命令执行成功后，系统会对本租户发起全量数据备份；

全量备份过程中，可以实时查看全量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


### 2 发起增量数据备份
增量备份是从上一次全量备份或增量备份开始，备份所有修改后的宏块，即仅针对已备份数据的增量部分进行备份。例如，假设当前租户最近一次是全量备份数据 `full_backup_set`，系统就会备份最近一次`full_backup_set` 之后修改的所有宏块；如果租户最近一次是增量备份数据 `inc_backup_set`，则系统就会备份最近一次 `inc_backup_set` 之后修改的所有宏块；

#### 2.1 使用限制及注意事项

- 执行增量数据备份需要确保已经有全量数据备份存在，如果在不存在全量数据备份的情况下直接发起增量备份，系统默认会将增量备份转换为全量备份。
    
- 对于低版本集群升级到高版本集群的场景，即使当前租户在低版本中已经执行了全量备份，升级到高版本的集群中后，如果直接发起增量备份，系统默认也会将增量备份转换为全量备份。
    
#### 2.2 发起增量备份
完成准备工作后，您可以通过以下方式发起增量数据备份。

假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`。


##### 2.2.1 系统租户发起增量备份
`sys` 租户可以对集群中的所有租户或指定租户发起增量数据备份；

备份并发度由租户级配置项 `ha_low_thread_score` 控制，该配置项用于指定备份、备份清理等中低优先级别任务队列使用线程时间片的权重值，取值范围为 [0, 100]，默认值为 `0`。在开始备份前，可以适当提高配置项 `ha_low_thread_score` 的值，建议每次将数值翻倍。查看配置项 `ha_low_thread_score` 的相关操作，请参见 [查看数据备份相关参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218411)，；

有关配置项 `ha_low_thread_score` 的详细说明，请参见 [ha_low_thread_score](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220343)，；
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2.（可选）设置租户的备份并发度
	-- 设置集群中所有租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all_user;
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all;

	-- 设置集群中指定租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = mysql_tenant;
```

> [!NOTE] `说明`：
> OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；

```sql
-- 3. 执行以下语句，发起增量数据备份
	-- 对集群中的所有租户发起增量数据备份
		-- 该方式会对集群中的所有租户发起增量数据备份;
		obclient [(none)]> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;
			-- 命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 和 `oracle_tenant` 租户发起增量数据备份；

	-- 对集群中指定租户发起增量数据备份
		-- 该方式仅对指定租户发起增量数据备份，不会影响集群中的其他租户；
		-- 对 `mysql_tenant` 租户发起增量数据备份的示例如下：
		obclient [(none)]> ALTER SYSTEM BACKUP INCREMENTAL TENANT = mysql_tenant;
			-- 同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；
```
命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 租户发起增量数据备份，；
增量备份过程中，可以实时查看增量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


##### 2.2.2 用户租户发起增量备份
用户租户可以对本租户发起增量数据备份，不会影响其他租户；

备份并发度由租户级配置项 `ha_low_thread_score` 控制，该配置项用于指定备份、备份清理等中低优先级别任务队列使用线程时间片的权重值，取值范围为 [0, 100]，默认值为 `0`。在开始备份前，可以适当提高配置项 `ha_low_thread_score` 的值，建议每次将数值翻倍。查看配置项 `ha_low_thread_score` 的相关操作，请参见 [查看数据备份相关参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218411)，；

有关配置项 `ha_low_thread_score` 的详细说明，请参见 [ha_low_thread_score](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220343)，；

```sql
-- 1. 租户管理员登录到数据库；
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.（可选）设置备份并发度；
obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10;
```

```sql
-- 3. 执行以下语句，发起增量数据备份
obclient [(none)]> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;
```
命令执行成功后，系统会对本租户发起增量数据备份，；
增量备份过程中，可以实时查看增量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


### 3 停止备份
发起数据备份后，您可以停止正在运行的备份任务。停止备份后，如果开启了自动清理备份的功能，则备份清理任务会及时清理不完整的备份；

#### 3.1 前提条件

由于停止正在运行的备份任务后，系统会产生一些不完整的备份数据。建议您提前开启自动清理备份功能，及时清理不完整的备份，以免占用资源。开启自动清理备份功能的相关操作，请参见 [自动清理过期备份](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218393)。

#### 3.2 背景信息

假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`，且租户 `mysql_tenant` 和 `oracle_tenant` 都正在进行数据备份。

#### 3.3 租户停止备份
````tab
tab: 系统租户停止备份
`sys` 租户可以停止集群中所有租户或指定租户的数据备份；
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2.执行以下语句，停止数据备份；
	-- 停止集群中所有租户的数据备份
	obclient [(none)]> ALTER SYSTEM CANCEL BACKUP;
		-- 该方式会停止集群中所有租户的数据备份任务；

	-- 停止集群中指定租户的数据备份
	obclient [(none)]> ALTER SYSTEM CANCEL BACKUP TENANT = mysql_tenant;
		-- 该方式仅停止指定租户正在执行的数据备份任务，不会影响集群中的其他租户；
		-- 同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；
```

tab: 用户租户停止备份
用户租户可以停止本租户正在执行的数据备份任务，不会影响其他租户；
```sql
-- 1.租户管理员登录到数据库；
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.执行以下语句，停止数据备份
	obclient [(none)]> ALTER SYSTEM CANCEL BACKUP;
```
````


## 1 参考文档
1. *发起全量数据备份*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218406)，；
2. *发起增量数据备份*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218410)，；
3. *停止备份*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218413)，；



