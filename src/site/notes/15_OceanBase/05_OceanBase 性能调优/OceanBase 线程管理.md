---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/OceanBase 线程管理/","dgPassFrontmatter":true}
---


### OceanBase 线程管理
包括*租户线程管理*，*系统后台线程管理*；

#### 1 租户线程管理
OceanBase V4 版本之前，不同租户间的 CPU 资源使用不是物理的隔离，而是通过对线程的控制达到的一种逻辑隔离；OceanBase 的线程管理和配置决定了租户的最大并发度和后台任务的性能；  
  
##### 1.1 工作线程数  
处理 SQl 和事务请求的线程被为工作线程；在 CREATE RESOURCE UNIT 时，需要指定租户资源规格的 min_cpu 与 max_cpu；

租户的工作线程受制于租户资源规格与以下参数的控制：  
1. `cpu_quota_concurrency`，每个 CPU 配额所允许的最大并发数；  
2. `workers_per_cpu_quota`，分配给每个 CPU 配额的工作线程数量的最多线程数；  
	1. 租户的工作线程数由以下公式计算而得：  
		1. 最大线程数 = max_cpu \* workers_per_cpu_quota  
		2. 活跃线程数 = min_cpu \* cpu_quota_concurrency  
  
最大线程数决定了一个租户可以创建的线程数；当租户收到 SQL 请求时，会从线程池中申请工作线程，如果线程池中没有可用线程且已分配的线程数小于最大线程数，则生成新的线程，分配给 SQL 执行的线程就是活跃线程；当活跃线程因为种种原因被挂起时，线程被放入调度队列等待，变为挂起线程；  
  
*workers_per_cpu_quota* 决定了租户的最大可分配线程，不影响租户的活跃线程数，通常不需要调整；  
  
*cpu_quota_concurrency* 的设置与一个 CPU 可以支持的并发线程数有关，不同的 CPU 芯片类型对于多线程的支持能力是不同的，不同的 SQL 类型也影响 CPU 的并发线程的支持能力；

我们建议遵循以下原则来进行调优：  
1. 对于 x86 架构，建议设置 *cpu_quota_concurrency* =4；对于 RAM 架构，建议设置 *cpu_quota_concurrency* =2；  
2. 对于只有大租户的集群，可以适当调低 *cpu_quota_concurrency* 的值；对于只有小租户的集群，可以适当调高 *cpu_quota_concurrency* 的值；  
3. 一个 CPU 可以支撑的并发线程数与 SQL 执行中的 CPU 耗时占比有关，SQL 的 CPU 耗时占比越高，一个 CPU 可以支撑的并发线程数越少；可以根据业务 SQL 的特征，相应地调整 *cpu_quota_concurrency* 的值；  
  
  
##### 1.2 大查询线程  
当数据库系统中同时存在 OLTP 与 OLAP 两种业务场景时，可能会出现一种极端的场景，执行较慢的 OLAP 业务把所有的工作线程都给占用了，导致执行较快的 OLTP 业务无法获得工作线程而排队；OceanBase 数据库可以识别大查询，并限制大查询的线程数，从而避免这种问题的发生；  


###### 1.2.1 大查询线程定义  
大查询线程是租户工作线程的一部分，OceanBase 数据库会根据以下参数的设置来识别和调度大查询；  
系统参数 *large_query_threshold* 定义了以查询时长作为维度的大查询判别方式，默认值为 5s，即查询时间超过 5s 的查询被认为是大查询；  
系统参数 *large_query_worker_percentage* 定义了大查询可以使用的工作线程数量，默认为租户线程的 30%；  
  
###### 1.2.2 大查询线程数调优  
假如一个 OBServer 服务器总的 CPU 数为 64，其中一个大规格租户的 CPU 配额为 32，根据默认的参数设置，我们可以计算得到以下结果：  
`最大线程数` = *max_cpu * workers_per_cpu_quota = 32 * 10 = 320*  
`活跃线程数` = *min_cpu * cpu_quota_concurrency = 32 * 4 = 128*  
`大查询线程数` = *活跃线程数 * large_query_worker_percentage = 128 * 30% = 38.4*  
  
如果在该租户中，某类业务的 SQL 耗时超过 5s，被识别为大查询，当该类业务的并发数比较高时，最多可以使用的活跃线程数为 38 个，租户还有 90 个活跃线程给其他执行时间小于 5s 的查询；但是，如果应用侧该类业务的并发度很高，且每个 SQL 的耗时主要都是 CPU 时间，实际上该业务对整个服务器的 CPU 使用可以达到 38/64 = 60%，远远超过我们逻辑上分配给大查询的 CPU 占比（租户的 CPU 占比 * 30% = 32/64 * 30% = 15%）；在实际的生产运行中，我们经常看到类似的场景导致 OBServer CPU 用满的问题；  
  
我们考虑另外一种场景，某类业务的 SQL 耗时大约 2s，没有被识别为大查询，当该业务突然大量并发执行时，会发生什么？租户的 128 个活跃线程有可能完全被该业务占用，导致其他业务无法执行；如果该 SQL 执行的主要耗时也是 CPU 时间，那么 128 个并发执行很可能直接就把 OBServer 的 64 个 CPU 抢占完了，导致 OBServer 的 CPU 用满，整个集群的性能都会受到影响；  
  
因此，对于存在高并发的慢 SQL 执行的联机交易系统，我们建议调整相关参数来限制大查询的 CPU 使用：  
1. 降低大查询线程的数量，把 *large_query_worker_percentage* 降为 20% 甚至 10%；  
2. 降低大查询的阈值，把 large_query_threshold 降为 2s 甚至 1s；


###### 1.2.3 大查询线程监控  
如何知道租户的工作线程数是否够用？当前有多少大查询因为大查询线程数的限制而排队？我们可以通过 OBServer 日志来进行监控；OBServer 每隔 10s 会在日志中打印队列的统计信息，在 observer.log 中搜索 '*dump tenant info*' 关键字，可以获得大查询线程的调度统计信息；  
  

```shell
cd /home/admin/oceanbase/log  
grep 'dump tenant.*tenant={id:1002' observer.log | sed "s/,/\n/g"  

## 字段含义：
	id，租户ID；  
	unit_min_cpu，保证为租户提供的最小 CPU 核数；  
	unit_max_cpu，限制租户最大 CPU 核数上限；  
	slice，无意义；  
	slice_remain，无意义；  
	token_cnt，调度器分配的 token 数，一个 token 会转换为一个工作线程；  
	ass_token_cnt，租户当前确认的 token 数（根据 token_cnt 确认，一般两者相等）；  
	lq_tokens，Large Query token 个数，根据 token_cnt 乘以大请求比列设置；  
	used_lq_tokens，当前持有 LQ Token 的 Worker 数；  
	stopped，租户 unit 是否正在删除；  
	idle_us，一轮日志 dump (10秒) 中工作线程空闲的总时间和；只统计等待队列的时间；  
	recv_hp_rpc_cntrecv_np_rpc_cntrecv_lp_rpc_cnt，租户累计收到不同级别的 rpc 请求数，其中 hp 表示 Highnp，Normallp 表示 Low;  
	recv_mysql_cnt，租户累计收到的 MySQL 请求数；  
	recv_task_cnt，租户累计收到的内部任务数；  
	recv_large_req_cnt，租户累计预判的大请求数，只会递增，不会清零；实际是重试的时间递增的；  
	tt_large_quries，租户累计处理的大请求数，只会递增，不会清零；实际是打点 check 的时间递增的；  
	actives，活跃工作线程数，一般和 workers 相等；actives 与 workers 的差表示租户工作线程缓存带工作线程的大请求缓存；  
	workers，租户持有的工作线程数，实际就是 workers 列表的大小；  
	lq waiting workers，处于等于调度的工作线程；  
	req_queue，不同优先级的工作队列，数字越小优先级越高；  
	large queued，当前预判出的大请求个数；  
```

  
##### 1.3 PX 线程  
PX 线程是 OceanBase 使用 PX 并行框架对 SQL 做并行执行的线程；OceanBase 对并行查询的并行线程做总量控制，并行线程数与租户的资源规格有关，由以下参数控制：  
1. `parallel_servers_target`：租户在每个 OB 节点上可申请的并行执行线程数量；  
	1. *parallel_servers_target* 是一个租户级变量，限定了该租户在任一 OBServer 上最多可以分配的并行线程数；
	2. 当 parallel_servers_target 使用默认值 0 时，OceanBase 会根据 *px_workers_per_cpu_quota* 和租户的资源规格来计算实际的 parallel_servers_target 值：
		1. *parallel_servers_target = min_cpu * px_workers_per_cpu_quota* ；
2. `px_workers_per_cpu_quota`：每个 CPU 上可以分配的并行执行线程数；  
  

关于 PX 线程的调优建议：  
1. 在 OLAP 场景，我们不建议大量使用并行查询，建议降低 px_workers_per_cpu_quota 的值（比如5），或者设置一个较小的 parallel_servers_target 值，限制总的并行线程数，以免正常的 TP 业务受到影响；  
2. 在 OLAP 场景，建议设置较大的并发线程数，比如将 px_workers_per_cpu_quota 设置为 20；  
  
  
#### 2 系统后台线程管理  
通常，用户不需要关注数据库系统的后台线程；但是在压测场景，系统压力过大时因为后天线程处理能力不足可能会导致系统的性能问题，甚至故障；因此，我们有必要了解系统后台线程以及调优方法：  
  
OBServer 的后台线程主要有以下几类：  
1. net io，处理网络 io 的线程（RPC）；  
2. disk io，处理磁盘 io 的线程；  
3. dag 线程，用于 partition 的转储，合并，迁移等任务的执行；  
4. clog writer，写 clog 的线程；  
5. election workers：选举线程；  
6. misc timer，包括多个后台定时器线程，主要负责清理资源；  
7. RootServer 专有的线程，比如 DDL 创建，负载均衡，备份恢复等；  
  
##### 2.1 RPC 线程调优  
OceanBase 集群内数据库节点间的网络通信由 RPC 线程来完成，RPC 线程的资源不足会导致数据库节点间的 RPC 延时过高，可能对 OceanBase 数据库造成以下影响：  
1. OceanBase 数据库的两阶段提交过程，需要 1 次日志延迟和 2次 RPC 延迟，最终体现在返回给应用层的 commit latency；  
2. OceanBase 数据库在全局的快照以及分布式一致性读等需要用 GTS 读取全局时间戳的场景，除了 GTS 本身的服务能力上限性能瓶颈，还有网络 RPC 延迟的开销；  
3. Clog 通过 Paxos 协议的多数派写入磁盘的过程，需要远端的 Clog 写入磁盘后进行反馈，最终体现于落盘延迟；  
4. 在网络包延迟严重的情况下，follower 副本不能及时收到最新的 clog，会不断发送 clog 传输请求给 leader 副本，这导致 leader 副本收到的 clog 请求次数明显超过正常的次数，这种情况也称为 clog 流量放大；  
  
###### 2.1.1 相关系统参数  
与 RPC IO 线程相关的参数主要有：  
1. `net_thread_count`，RPC IO 线程数； 
2. `net_thread_count`：接收客户端请求的网络线程数；
	1.  【_集群级_】【】，取值范围为 [0, 16]，默认值为 10；动态配置项，修改后直接生效；
	2.  该参数并不是越大越好，太高反而会增加线程切换的消耗，影响性能；根据实验室压测的结果，通常我们不建议将该参数设置为大于 24 的值；在实践中，可以 top -H 查看 MySQL IO 线程的使用效率，如果使用大于 90%，说明接收请求的线程可能成为瓶颈，建议提高该参数，如果所有线程都小于 50%，建议减小该参数，减低线程切换的开销
3. `__easy_memory_limit`，OBServer 的 rpc packet 内存上限；
	1. 【】；默认值为 4G；
4. `default_transport_compress_func`，RPC 的压缩算法； 
5. `default_transport_compress_func`: 用于设置整个集群的 RPC 压缩算法；
	1.  【_集群级_】【字符串】；默认值为：none；动态生效；详细情况：[default_transport_compress_func V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944973)，；
  
net_thread_count 的默认值值为 0，OceanBase 自动根据系统的 CPU 数来设置 RPC IO 线程数，计算公式为：max[6,CPU_NUM / 8]，其中 CPU_NUM 为机器的逻辑 CPU 核数；比如一个 64C 的服务器，默认的 RPC IO 线程数为 8；  
  
RPC IO 的性能主要与以下因素相关：  
1. RPC IO 线程的数量，内存大小是否满足当前业务需求；  
2. 网卡绑定策略与网卡的性能是否满足当前业务需求；若 OBServer 所在的服务器具备多个网卡，我们要确保网卡使用了正确的绑定策略，多个网卡共同承担流量；  
3. 网卡软中断（IPQ）在各个 CPU 核的 CPU 使用是否均衡，是否存在少数 CPU 核上 si CPU 使用占比过高，导致 CPU 争取的问题；


###### 2.1.2 如何监控 RPC IO 的性能  
通过 OCP 监控可以统计 RPC 的 IO 吞吐量和平均的耗时；  
正常情况下，RPC 的平均耗时不会超过 200us；在压测时，如果发现 RPC 的吞吐量不能随着并发数上升而增长，或者 RPC 的平均耗时随着并发数上升而急剧增加（比如 达到 1ms），则需要判断 RPC IO 线程是否存在线程数或内存的瓶颈，或者网卡软中断是否集中在少数几个 CPU 上导致 CPU 使用紧张；  
  
###### 2.1.3 如何监控 RPC 线程数是否够用  
方法1：使用 top 命令查看 RPC IO 线程  
```
1.top 命令查看 OBServer 的线程 CPU 使用情况  
top Hp observer_pid，其中 observer_pid 为 OB Server 进程 ID  
  
2.在 top 界面筛选名字为 RpcIO 的线程：线程数量应该与 net_thread_count 的设置相同；  
  
3.若线程的使用效率大于 90%，则说明接收请求的线程可能成为了瓶颈，需要增加 RPC IO 线程的数量；  
```

  
方法2：通过 OBServer 日志查看 RPC IO 线程的使用统计  
```
observer.log 会定期打印 RPC EASY STAT 信息，包含了每个 RPC IO 线程的统计；  
  
request done = in_requests_done / out_requests_done  
request doing = in_requests_doing / out_requests_doing  
  
若 RPC IO 线程的 request doing 的 in 和 out 不都为 0，说明该线程处于工作状态；若系统所有 RPC IO 线程都处于工作状态，则说明 RPC 线程比较繁忙，需要增加 RPC IO 线程的数量； 
```
 
  
###### 2.1.4 如何监控 RPC 线程的内存是否够用  
observer.log 会定期打印各个租户的内存使用情况，RPC 线程归属于 500 租户，500 租户中的 ctx_id = LIBEASY 的内存统计即为 RPC 线程的内存使用统计；  
  
若 used 内存始终保持高位，接近 `__easy_memory_limit`，则需要调大 `__easy_memory_limit` 的值；  
  
###### 2.1.5 如何查询网卡软中断的 CPU 使用  
通过 top 1 命令，可以查看每个 CPU 的相关统计信息，关于网卡软中断，主要关注 si 这一列的值；  
  
使用 antman 或者 OAT 工具部署 OBServer 服务器时，安装过程中会对网卡 IRQ 进行 CPU 绑定，把网卡的 IRQ 均匀绑定到一定数量的 CPU 核上，避免 IRQ 聚集在某几个 CPU 上，产生性能瓶颈；  
  
我们建议 si 的比例在任一 CPU 核上均小于 20%；如果 antman 初始化绑定的 CPU 不够分散，可以直接修改 total_core_num 的数量，比如指定 total_core_num = 16，然后手动执行 set_nic_irq_ob.sh 来把网卡的软中段打散到更多的 CPU 核上，提升网卡 IRQ 的处理速度；


###### 2.1.6 RPC IO 线程调优的建议  
1. net_thread_count 并不是越大越好，太高反而会增加线程切换的消耗，影响性能；根据实验室压测的结果，通常我们不建议把 net_thread_count 设置为大于 24 的值；  
2. 默认的 `__easy_memory_limit` 值（4G）可能不能满足大查询场景的需要，在生产环境我们建议调大该参数值，比如设置为 10G 或 20G；  
3. 监控网卡 IRQ 的 CPU 使用是否均衡，如果 IRQ 聚集在少数几个 CPU 使用率很高，建议把网卡 IRQ 绑定到更多的 CPU 核上；  
4. 准对网络带宽紧张，CPU 资源比较充裕的业务场景，可以统一打开 RPC 的压缩算法，节省网络带宽；  
  
  
##### 2.2 负载均衡线程调优  
在 OceanBase 分布式数据库中，RS 的负载均衡（balancer）模块使用 rebalance task 执行负载均衡，以及全局索引创建和数据备份时的分区数据的拉取；Rebalance task 本质是执行 data copy 的工作，把副本的数据从一个 OBServer 复制到另一个 OBServer；  
  
如果系统中有大量的 rebalance task 要执行，比如在集群扩容时的新建副本的操作，rebalance task 的并行线程数以及网络带宽的大小对整个操作的耗时起着决定性作用；  
  
###### 2.2.1 相关系统参数  
Rebalance task 的并行线程数由以下参数控制：  
1. `data_copy_concurrency`，整个集群内数据库迁移复制任务的最大并发数；  
2. `server_data_copy_in_concurrency`，单个节点上数据迁入任务的最大并发数；  
3. `server_data_copy_out_concurrency`，单个节点上数据迁出任务的最大并发数；  
  
其他影响 rebalance task 性能的系统参数：  
1. `migrate_concurrency`，所有数据迁出任务 copy 宏块数据的并发线程总数；  
2. `sys_bkgd_net_percentage`，迁移，复制，rebuid，备份以及恢复等后台任务的网络带宽占用百分比；  
  
###### 2.2.2 不同场景下的参数设置  

```shell
同机房网络：  
	data_copy_concurrency = 100  
	server_data_copy_out_concurrency = 10  
	server_data_copy_in_concurrency = 10  
	migrate_concurrency = 10  
	sys_bkgd_net_percentage = 90  
  
跨机房网络：1024MB/s  
	带宽跑满的场景下：所有参数使用默认配置  
	带宽跑不满的场景下：  
		sys_bkgd_net_percentage = 80  
		migrate_concurrency = 20  
  
跨机房网络：512MB/s  
	使用默认配置；  
  
跨机房网络：100MB/s  
	除以下参数外，均使用默认配置：  
		server_data_copy_out_concurrency = 1  
		server_data_copy_in_concurrency = 1  
		migrate_concurrency = 1  
```

  
假如：一个两个三中心五副本的集群，同城两中心的网络带宽为 40Gb/s，异地带宽是 1Gb/s；根据上表的建议，我们应该为集群中的各个 OBServer 设置不同的参数配置；  
1. 同城四副本（zone1 ~ zone4）按照同机房带宽来配置，因为两中心的带宽非常高；  
2. 异地第五副本（zone5）按照跨机房网络带宽 100MB/s 来配置，因为两地的带宽很低；  
  

```sql
/* 数据 copy 并发 */  
ALTER SYSTEM SET data_copy_concurrency = 100;  
  
/* 服务器上数据库传出/传入并发度 */  
ALTER SYSTEM SET server_data_copy_out_concurrency = 10 zone = 'zone1';  
ALTER SYSTEM SET server_data_copy_out_concurrency = 10 zone = 'zone2';  
ALTER SYSTEM SET server_data_copy_out_concurrency = 10 zone = 'zone3';  
ALTER SYSTEM SET server_data_copy_out_concurrency = 10 zone = 'zone4';  
ALTER SYSTEM SET server_data_copy_out_concurrency = 1 zone = 'zone5';  
  
ALTER SYSTEM SET server_data_copy_in_concurrency = 10 zone = 'zone1';  
ALTER SYSTEM SET server_data_copy_in_concurrency = 10 zone = 'zone2';  
ALTER SYSTEM SET server_data_copy_in_concurrency = 10 zone = 'zone3';  
ALTER SYSTEM SET server_data_copy_in_concurrency = 10 zone = 'zone4';  
ALTER SYSTEM SET server_data_copy_in_concurrency = 1 zone = 'zone5';  
  
/* 每个数据复制任务拉取宏块的并行度 */  
ALTER SYSTEM SET migrate_concurrency = 10 zone = 'zone1';  
ALTER SYSTEM SET migrate_concurrency = 10 zone = 'zone2';  
ALTER SYSTEM SET migrate_concurrency = 10 zone = 'zone3';  
ALTER SYSTEM SET migrate_concurrency = 10 zone = 'zone4';  
ALTER SYSTEM SET migrate_concurrency = 1 zone = 'zone5';  
```

  
此外，针对一些具体的场景，我们也可以按照以下建议来设置相关参数：  
1. 在 OBServer 节点替换时，若本 Zone 内已经做过补副本的操作，节点替换后将在本 Zone 内进行副本的迁移，为了提升同机房的副本迁移速度，应按照同机房网络来设置该 Zone 的相关配置参数；  
2. 在主备库 Failover 的场景，为了加快备集群 Failover 的速度，我们需要修改以下参数来提升 rebalance task 与转储合并任务的性能；  
  
建议配置项设置：  
```sql
_mini_merge_concurrency = 16，转储的并行度从默认值 3 修改为 16；  
_ob_minor_merge_schedule_interval，用于调度转储的间隔时间，从默认值 20s 修改为 3s；  
data_copy_concurrency = 600，备集群最多有 600 个数据迁移复制任务；  
server_data_copy_out_concurrency = 300，每台 OBServer 最多有 300 个数据迁出任务；  
server_data_copy_in_concurrency = 300，每台 OBServer 最多有 300 个数据迁入任务；  
```

  
###### 2.2.3 监控 rebalance task 的监控  
使用以下系统表和视图来监控 rebalance task 的执行状态：  
1. `__all_rootservice_job`，记录迁移 unit，变 locality 等单条 SQL 对应的任务状态；  
	1. [[15_OceanBase/查询 Root Service 任务记录\|查询 Root Service 任务记录]]，；
2. `__all_rootservice_event_history`，可以查看负载任务发起的时间及执行结果；  
3. `__all_virtual_replica_task`，可以查看待发起或正在执行的副本相关的增，删和负载均衡操作；  
4. `__all_virtual_rebalance_task_stat`，可以查看集群中当前已经生成的所有负载均衡任务；  
5. `__all_virtual_sys_task_status`，可以查看下发到 OBServer 端执行的系统任务，包括负载均衡任务；





### 参考文档
1. 


