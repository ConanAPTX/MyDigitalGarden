---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/gv, sql_audit 视图/","dgPassFrontmatter":true}
---


### gv\$sql_audit，gv\$ob_sql_audit
#### 1 功能
*GV\$SQL_AUDIT*：展示所有 Server 上每一次 SQL 请求的来源、执行状态等统计信息；该视图是按照租户拆分的，除了系统租户，其他租户不能跨租户查询；

> [!NOTE] 修改记录：
> 该视图从 V4.0.0 版本开始视图名由 `v$sql_audit` 调整为 `v$ob_sql_audit`；

#### 2 相关视图
`__all_virtual_sql_audit`：；


#### 3 常用查询

```sql


```


#### 4 字段说明
1. 123
	1. `SVR_IP`：varchar(46)，NO，IP 地址；
	2. `SVR_PORT`：bigint(20)，NO，端口号
	3. `REQUEST_ID`：bigint(20)，NO，请求的 ID 号
	4. `SQL_EXEC_ID`：bigint(20)，NO，本次执行的 ID
	5. `TRACE_ID`：varchar(128)，NO，该语句的 trace_id
	6. `SID`：bigint(20) unsigned ，NO，Session 连接 ID
	7. `CLIENT_IP`：varchar(46)，NO，
		1. 当客户端通过 ODP 与 OBServer 节点连接时，表示转发请求的 ODP 的 IP
		2. 当客户端直接与 OBServer 节点连接时，表示发送请求的客户端的 IP
	9. `CLIENT_PORT`：bigint(20) ，NO，发送请求的客户端端口号
	10. `TENANT_ID`：bigint(20) ，NO，发送请求的租户 ID
	11. `TENANT_NAME`：varchar(64) ，NO，发送请求的租户名称
	12. `EFFECTIVE_TENANT_ID`：bigint(20) ，NO，租户 ID
	13. `USER_ID`：bigint(20)  ，NO，发送请求的用户 ID
	14. `USER_NAME`：varchar(64) ，NO，	发送请求的用户名称
	15. `USER_GROUP`：bigint(20) 	YES 	用户所属资源组 ID
	16. `USER_CLIENT_IP`：varchar(46) ，NO，发送请求的客户端的 IP
	17. `DB_ID`：bigint(20) unsigned ，NO，数据库 ID
	18. `DB_NAME`：varchar(128) ，NO，数据库名称
	19. `SQL_ID`：varchar(32) ，NO，该 SQL 的 ID
	20. `QUERY_SQL`：longtext ，NO，实际的 SQL 语句
	21. `PLAN_ID`：bigint(20) ，NO，	执行计划的 ID
	22. `AFFECTED_ROWS`：bigint(20) ，NO，影响行数
	23. `RETURN_ROWS`：bigint(20) ，NO，返回行数
	24. `PARTITION_CNT`：bigint(20)  ，NO，该请求涉及的分区数
	25. `RET_CODE`：bigint(20) ，NO，执行结果返回码：
		1. 0：表示没有错误，语句成功执行
		2. 其他返回码：表示 OceanBase 错误码。有关错误码的详细介绍，请参见 错误码
	26. `QC_ID`：bigint(20) unsigned ，NO，并行执行场景下，调度器 ID
	27. `DFO_ID`：bigint(20) ，NO，并行执行场景下，当前执行的子计划 ID
	28. `SQC_ID`：bigint(20) ，NO，并行执行场景下，本地协调器 ID
	29. `WORKER_ID`：bigint(20) ，NO，	并行执行场景下，工作线程 ID
	30. `EVENT`：varchar(64) ，NO，最长等待事件名称
	31. `P1TEXT`：varchar(64) ，NO，	等待事件参数 1
	32. `P1`：bigint(20) unsigned ，NO，等待事件参数 1 的值
	33. `P2TEXT`：varchar(64) ，NO，等待事件参数 2
	34. `P2`：bigint(20) unsigned ，NO，等待事件参数 2 的值
	35. `P3TEXT`：varchar(64) ，NO，等待事件参数 3
	36. `P3`：bigint(20) unsigned ，NO，等待事件参数 3 的值
	37. `LEVEL`：bigint(20) ，NO，等待事件的级别
	38. `WAIT_CLASS_ID`：bigint(20) ，NO，等待事件所属的 Class ID
	39. `WAIT_CLASS`：bigint(20) ，NO，等待事件所属的 Class 的下标
	40. `WAIT_CLASS`：varchar(64) ，NO，等待事件所属的 Class 名称
	41. `STATE`：varchar(19) ，NO，等待事件的状态
	42. `WAIT_TIME_MICRO`：bigint(20) 	NO 	该等待事件所等待的时间，单位：微秒
	43. `TOTAL_WAIT_TIME_MICRO`：bigint(20) 	NO 	执行过程所有等待的总时间，单位：微秒
	44. `TOTAL_WAITS`：bigint(20) 	NO 	执行过程总等待的次数
	45. `RPC_COUNT`：bigint(20) 	NO 	发送的 RPC 个数
	46. `PLAN_TYPE`：bigint(20) 	NO 	执行计划类型：local；remote；distribute；
	47. `IS_INNER_SQL`：tinyint(4) 	NO 	是否为内部 SQL 请求
	48. `IS_EXECUTOR_RPC`：tinyint(4) 	NO 	当前请求是否为 RPC 请求
	49. `IS_HIT_PLAN`：tinyint(4) 	NO 	是否命中 plan_cache
	50. `REQUEST_TIME`：bigint(20) 	NO 	开始执行时间点，单位：微秒
	51. `ELAPSED_TIME`：bigint(20) 	NO 	接收到请求到执行结束所消耗的总时间，单位：微秒
	52. `NET_TIME`：bigint(20) 	NO 	发送 RPC 到接收到请求的时间，单位：微秒
	53. `NET_WAIT_TIME` 	bigint(20) 	NO 	接收到请求到进入队列的时间，单位：微秒
	54. `QUEUE_TIME`：bigint(20) 	NO 	请求在队列的等待时间，单位：微秒
	55. `DECODE_TIME`：bigint(20) 	NO 	出队列后 Decode 的时间，单位：微秒
	56. `GET_PLAN_TIME`：bigint(20) 	NO 	开始 Process 到获得计划的时间，单位：微秒
	57. `EXECUTE_TIME`：bigint(20) 	NO 	计划执行所消耗的时间，单位：微秒
	58. `APPLICATION_WAIT_TIME`：bigint(20) unsigned 	NO 	所有 Application 类事件的总时间，单位：微秒
	59. `CONCURRENCY_WAIT_TIME`：bigint(20) unsigned 	NO 	所有 Concurrency 类事件的总时间，单位：微秒
	60. `USER_IO_WAIT_TIME`：bigint(20) unsigned 	NO 	所有 user_io 类事件的总时间，单位：微秒
	61. `SCHEDULE_TIME`：bigint(20) unsigned 	NO 	所有 Schedule 类事件的时间，单位：微秒
	62. `ROW_CACHE_HIT`：bigint(20) 	NO 	行缓存命中次数
	63. `BLOOM_FILTER_CACHE_HIT`：bigint(20) 	NO 	Bloom Filter 缓存命中次数
	64. `BLOCK_CACHE_HIT`：bigint(20) 	NO 	块缓存命中次数
	65. `DISK_READS`：bigint(20) 	NO 	物理读次数
	66. `RETRY_CNT`：bigint(20) 	NO 	重试次数
	67. `TABLE_SCAN`：tinyint(4) 	NO 	判断该请求是否含全表扫描
	68. `CONSISTENCY_LEVEL`：bigint(20) 	NO 	一致性级别
	69. `MEMSTORE_READ_ROW_COUNT`：bigint(20) 	NO 	MemStore 中的读行数
	70. `SSSTORE_READ_ROW_COUNT`：bigint(20) 	NO 	SSStore 中连读的行数
	71. `DATA_BLOCK_READ_CNT`：bigint(20) 	NO 	访问的数据微块数量
	72. `DATA_BLOCK_CACHE_HIT`：bigint(20) 	NO 	命中数据微块cache数量
	73. `INDEX_BLOCK_READ_CNT`：bigint(20) 	NO 	访问的中间层微块数量
	74. `INDEX_BLOCK_CACHE_HIT`：bigint(20) 	NO 	命中中间层微块cache数量
	75. `BLOCKSCAN_BLOCK_CNT`：bigint(20) 	NO 	单边扫描的数据微块数量
	76. `BLOCKSCAN_ROW_CNT`：bigint(20) 	NO 	单边扫描的数据行数
	77. `PUSHDOWN_STORAGE_FILTER_ROW_CNT`：bigint(20) 	NO 	下压存储filter过滤后行数
	78. `REQUEST_MEMORY_USED`：bigint(20) 	NO 	该请求消耗的内存
	79. `EXPECTED_WORKER_COUNT`：bigint(20) 	NO 	请求期望的工作线程数
	80. `USED_WORKER_COUNT`：bigint(20) 	NO 	请求实际使用的工作线程数
	81. `SCHED_INFO`：varchar(16384) 	YES 	请求的调度信息
	82. `FUSE_ROW_CACHE_HIT`：bigint(20) 	NO 	暂不支持该字段，字段默认为 NULL
	83. `PS_CLIENT_STMT_ID`：bigint(20) 	NO 	请求对应的 Prepare ID：
		1. -1：表示 SQL 语句没有使用 PS 协议
		2. 非 -1：表示 SQL 语句使用了 PS 协议，为 PS 协议在客户端对该语句返回的唯一标志
	84. `PS_INNER_STMT_ID`：bigint(20) 	NO 	请求对应的 Prepare ID：
		1. -1：表示 SQL 语句没有使用 PS 协议
		2. 非 -1：表示 SQL 语句使用了 PS 协议，为 PS 协议在内部对该语句返回的唯一标志
	85. `TX_ID`：bigint(20) 	NO 	请求对应的事务 ID
	86. `SNAPSHOT_VERSION`：bigint(20) 	NO 	SQL语句使用的读快照版本
	87. `REQUEST_TYPE`：bigint(20) 	NO 	请求对应的类型：
		1. 0：表示非法；1：表示是一个内部请求；2：表示是一个本地请求，例如，Local 计划；3：表示远程请求；
		2. 4：表示分布式请求；5：表示 SQL 的 Prepare 请求；6：表示 SQL 的 Execute Stmt 请求；
	88. `IS_BATCHED_MULTI_STMT`：tinyint(4) 	NO 	是否进行 Batch Multi Stmt 的优化
	89. `OB_TRACE_INFO`：varchar(4096) 	NO 	用户设置的 Trace 信息
	90. `PLAN_HASH`：bigint(20) unsigned 	NO 	执行计划的 Hash 值
	91. `LOCK_FOR_READ_TIME`：bigint(20) 	NO 	读取数据时等待锁的耗时，单位：微秒
	92. `PARAMS_VALUE`：longtext 	NO 	PS 绑定的参数值
	93. `RULE_NAME`：varchar(256) 	NO 	规则名称
	94. `PARTITION_HIT`：tinyint(4) 	NO 	
		1. 若获得 DAS 执行计划，命中本地 Partition 则为 1，没有命中则为 0。
		2. 若未获得 DAS 计划，默认为 1。
	95. `TX_INTERNAL_ROUTING`：bigint(20) 	NO 	表示 SQL 语句是否经过了内部路由：
		1. 0：没有经过内部路由，即该 SQL 语句是直接在当前节点上执行的
		2. 1：经过内部路由，即该 SQL 语句在当前节点上被路由到了其他节点上执行
	96. `TX_STATE_VERSION`：bigint(20) unsigned 	NO 	表示 SQL 语句的事务状态版本
	97. `FLT_TRACE_ID`：varchar(1024) 	NO 	表示该条记录全链路诊断的 trace_id，如果为空，则说明没有被全链路诊断监控。该字段是Uuid，不同于 Trace。其表示形式类似于：000600d6-a5de-038c-6c80-df07e4e79149；
		1. 该字段从 V4.2.1 版本开始引入；

### 参考文档
1. `gv$sql_audit`：[MySql_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356331)，[Oracle_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356209)，；
2. `GV$OB_SQL_AUDIT`：[SYS_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219776)，[Oracle_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219093)，[MySql_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219838)，；


