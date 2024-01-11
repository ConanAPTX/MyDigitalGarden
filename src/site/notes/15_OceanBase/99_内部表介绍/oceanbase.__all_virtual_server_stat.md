---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_server_stat/","dgPassFrontmatter":true}
---


### oceanbase.__all_virtual_server_stat

#### 1 功能
`__all_virtual_server_stat` 内部表用于查询 OB Server 资源情况；  若利用该表查询内存情况会与 OCP 不一致，即 ocp 的 web 页面还是通过该表查的，但是这个有加一个 system_memory 的校正逻辑，使用内存 和 总内存 都有加这个 system_memory；  
  

```sql
-- 查询 system_memory 参数
SHOW PARAMETERS LIKE 'system_memory';  
```

#### 2 字段说明
```
oceanbase.__all_virtual_server_stat 内部表字段介绍：  
	svr_ip：varchar(32)，SERVER IP；  
	svr_port：bigint(20)，SERVER PORT；  
	zone：varchar(128)，SERVER 所在 ZONE；  
	cpu_total：double，SERVER 拥有总 CPU 个数；  
	cpu_assigned：double，SERVER 已经分配出去 CPU 个数；  
	cpu_assigned_percent：bigint(20)，SERVER CPU 已分配比例，等于 assigned/total；  
	mem_total：bigint(20)，SERVER 拥有的总内存量，该数值 + system_memory = OCP 查询到的值；  
	mem_assigned：bigint(20)，SERVER 已经分配出去的内存量，该数值 + system_memory = OCP 查询到的值；  
	mem_assigned_percent：bigint(20)，SERVER 内存已分配比例；  
	disk_total：bigint(20)，SERVER 磁盘总量；  
	disk_assigned：bigint(20)，SERVER 已分配磁盘量；  
	disk_assigned_percent：bigint(20)，SERVER 磁盘 已分配比例；  
	unit_num：bigint(20)，SERVER 上的 UNIT 数；  
	migrating_unit_num：bigint(20)，SERVER 上正在迁出的 UNIT 数；  
	merged_version：bigint(20)，SERVER 已经合并到的版本；  
	leader_count：bigint(20)，SERVER 上 LEADER 总个数；  
	load：double，SERVER 负载；  
	cpu_weight：double，SERVER CPU 权重，用于负载均衡计算；  
	memory_weight：double，SERVER 内存权重，用于负载均衡计算；  
	disk_weight：double，SERVER 磁盘权重，用于负载均衡计算；  
	id：bigint(20)，SERVER ID；  
	inner_port：bigint(20)，SERVER RPC 端口号；  
	build_version：varchar(256)，SERVER 源代码版本号；  
	register_time：bigint(20)，SERVER 注册上线时间；  
	last_heartbeat_time：bigint(20)，SERVER 最后一次获得 RS 心跳时间；  
	block_migrate_in_time：bigint(20)，SERVER 被禁止迁入的起始时间；  
	start_service_time：bigint(20)，SERVER 开始对外服务时间；  
	last_offline_time：bigint(20)，SERVER 最后一次下线时间；  
	stop_time：bigint(20)，SERVER 停止服务时间；  
	force_stop_heartbeat：bigint(20)，SERVER 是否是被主动停止心跳；  
	admin_status：varchar(64)，SERVER 管理状态，正常/删除中；  
	heartbeat_status：varchar(64)，SERVER 心跳状态；  
	with_rootserver：bigint(20)，SERVER 是否提供 RS 服务；  
	with_partition：bigint(20)，SERVER 上是否还有 PARTITION；  
	mem_in_use：bigint(20)，SERVER 内存使用量，未设置；  
	disk_in_use：bigint(20)，SERVER 磁盘使用量；  
	clock_deviation：bigint(20)，SERVER 时钟偏移；  
	heartbeat_latency：bigint(20)，SERVER 心跳延迟；  
	clock_sync_status：varchar(64)，SERVER 时钟同步状态；  
	cpu_capacity：；  
	cpu_max_assigned: ；  
	mem_capacity: ；  
	mem_max_assigned: ；  
	ssl_key_expired_time: ；
```



### 参考文档



