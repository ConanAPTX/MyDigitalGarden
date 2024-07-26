---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/80_OB 相关知识库_1_OceanBase/","dgPassFrontmatter":true}
---

### OceanBase 知识库

#### 1 OceanBase 数据库知识
[OceanBase 数据库知识导图](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000324072)，；

##### 1.1 安装部署

##### 1.2 连接管理
1. OceanBase 数据库支持动态调整数据库最大连接数吗？
2. 如何查看 OBProxy 的连接数
3. 连接数相关参数与异常场景说明
4. File Handle 对 OBProxy 连接数上限影响
5. 变更租户规格会导致租户最大仅支持 100 个连接
6. Perl 如何连接 OceanBase 数据库
7. OceanBase 数据库 MySQL 租户与 MySQL 数据库的密码校验方式的比较
8. 连接 OceanBase 数据库租户报 access denied
9. 升级 liboblog 版本后与 observer 无法建连
10. 如何排查 RPC 失败的原因
11. 客户端报错 Lost connection to MySQL server during query 的几种原因
12. easy_reqeust hold by upper-layer for too much time 耗时问题
13. 影响 OceanBase 数据库连接超时的相关配置
14. 如何排查 server 断连接问题
15. 如何配置 OceanBase 数据库的 socket 级故障检测超时时间
16. max_allowed_packet 与 SQL 长度的关系
17. 如何配置读写分离集群
18. OBServer 出现 force_disconnect 告警的触发逻辑和原因
19. OceanBase 集群出现 Failed to do check_ack_timeout 告警的原因
20. 空闲链接超时导致的应用周期性断链问题
21. OB_EXCEED_MEM_LIMIT 错误码 4080 产生原因
22. 与服务器的连接断开，错误代码 ERROR 2013
23. select a(a'') 断连接的问题
24. 如何统计单个 OBProxy 当前的连接数
25. easy_connection_on_timeout_conn 的问题原因
26. OBProxy 连接问题的排查步骤
27. JDBC 连接 OceanBase 数据库失败
28. 普通租户查询当前租户的所有会话数
29. SHOW PROXYSESSION 语句报错，错误码 4024
30. ret_code 5065 和 5066 的区别
31. 直连普通租户时遇到报错：Tenant not in this server
32. MySQL 驱动错误
33. 通过 JDBC 连接 OceanBase 数据库，获取连接超时
34. 网络线程关键打印汇总
35. Location cache 刷新机制
36. 刷新 location cache 的应急方法
37. 异步批量 location_cache 刷新任务不执行
38. OceanBase 集群会话多次 select connection_id() 出的 id 不一样
39. .net 驱动连接报错
40. tcp self connection 产生的原因和解决方法
41. Mybatis 升级后报错 maximum open cursors exceeded
42. OBClient 客户端工具如何支持多 IP 连接登录
43. OceanBase 数据库网络相关问题的排查方法
44. OceanBase 数据库中启用 pkt-nio 功能时 RPC 的 fly_ts 耗时长的原因
45. OceanBase 数据库网络速率配置方案
46. [连接异常相关问题的原因与解决方法](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000763298?back=kb)，；
	1. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.2 连接管理/通信链接失败：JDBC Communications link failure\|通信链接失败：JDBC Communications link failure]]，；
47. 连接报错 4016



##### 1.3 集群管理

##### 1.4 租户管理

##### 1.5 副本管理

##### 1.6 事务管理

##### 1.7 同步和选举
1. OceanBase 数据库选举
2. OceanBase 数据库的选举协议为何依赖于时钟同步 
3. OBServer 无主选举参数优化说明
4. 如何配置选举配置项 election_blacklist_interval
5. OceanBase 数据库中 RootService 服务常见问题
6. 如何查看 Root Service 切换完成的时间点
7. 排查是否因时钟跳变导致 RS 失去心跳的方法
8. RS 自检线程报错 thread hang
9. RS 启动失败在 ObTTLScheduler::start
10. 如何确认每个租户的 GTS 的 Leader 位置
11. `clog 相关问题`
	1. OceanBase 数据库 Clog 对本地存储的要求
	2. Clog 日志量统计方法
	3. CLOG 文件快速增长的问题分析
	4. OBServer 进程在日志盘每 10 秒有大写入原因
	5. 根据 current log_task status 日志判断 clog task 的状态
	6. 如何观察 OBServer 重启后 clog 回放进度
	7. 如何查看 clog 同步延迟？
	8. OCP 日志告警 clog 滑动窗口超时报错
	9. SYS 租户三个节点重启后 RS 无主 Clog 提示 ret=-4030 问题处理
	10. Truncate 分区后出现 clog 延迟与副本缺失的告警
	11. OBServer 告警：partition may be gc, but clog file still exist
	12. 表删除后未 GC 导致 Clog 无法回收的问题
	13. OceanBase 报错：read info block data failed(ret=-4070)
	14. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.7 同步和选举/修改 clog 磁盘占用百分比后 clog 未自动清理\|修改 clog 磁盘占用百分比后 clog 未自动清理]]，；
	15. Clog 盘满问题运维处理方法
	16. 大事务引起 clog 盘满处理
	17. OceanBase 数据库事务触发同步 CLOG 时机梳理
	18. Clog 盘满如何排查
	19. 日志未回调/同步超时问题排查
12. `切主和无主相关问题`
	1. OBServer 日志中关于分区无主的信息
	2. OceanBase 数据库无主状态的排查方法
	3. clog 上任超时导致无主
	4. R 副本切主后 location cache 无法刷新导致无主报错 4038
	5. OceanBase 数据库的切主优先级
	6. SYS 租户下建索引表导致 1 号表无主
	7. 如何手动执行切主
	8. 如何开启数据副本自动切主
	9. 如何诊断 OceanBase 数据库切主原因
	10. OceanBase 数据库部分租户切主失败问题排查步骤
	11. CLOG 写盘 HANG 导致切主问题
	12. 大事务导致内存分配失败, 导致切主失败
	13. 业务偶发收到 the observer or zone is not the master 原因
	14. 手动切主 Zone 到其他机房场景应急演练流程
	15. OBServer 切主后回放日志报错 ERROR 6205(OB_TRANS_INVALID_STATE)
	16. RTO 耗时长排查文档

##### 1.8 合并转储
1. *什么是冻结和转储*
2. *转储相关问题*
	1. OceanBase 数据库的分层转储功能
	2. 什么是转储预热
	3. OceanBase 数据库转储的常见问题
	4. OceanBase 数据库如何处理一台 OBServer 上多个重叠的冻结动作
	5. [OceanBase 如何查看距离上一次冻结过去的小时数](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209898?back=kb)，；
	6. undo_retention 介绍
	7. 转储错误，错误代码 4138/ORA-01555
	8. OB_MINOR_FREEZE_NOT_ALLOW(-4263)
	9. 集群节点转储卡住并且合并超时
	10. insert ignore 卡住转储
	11. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.8 合并转储/MemStore Freeze 操作慢的可能原因\|MemStore Freeze 操作慢的可能原因]]，；
	12. 如何判断转储队列积压
	13. 如何降低频繁转储的压力
	14. MemStore 的 freeze_trigger 低于设定值，导致频繁转储
	15. Too many frozen memtables ret=-4263
	16. 集群转储时报 -4016，且多次重试未自愈
3. *合并相关问题*
	1. 什么是合并 major freeze
	2. 什么是 mini minor merge
	3. [什么是 buffer minor merge](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209911?back=kb)，；
	4. 什么是轮转合并
	5. 什么是并行合并
	6. 什么是渐进合并
	7. OceanBase 数据库合并的常见问题
	8. 常见的 OceanBase 数据库 compaction 参数
	9. compaction_diagnose 视图使用指南
	10. 如何修改 OceanBase 数据库的合并方式
	11. 如何查看 Compaction 的当前合并进度及历史记录
	12. [集群中存在下线主机对合并的影响](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209896?back=kb)，；
	13. OBServer 合并期间是否可以做负载均衡
	14. io_resource.conf 文件对 OceanBase 数据库合并和 partition migrate 的影响
	15. 新增备集群后，为何合并时延加长？
	16. 合并时报错 -4006/-4343
	17. log item too long ret=-4019
	18. 合并错误，错误代码 4103
	19. minor merge 过程中丢更新最终导致副本间 checksum error
	20. 数据的 collation type 不一致导致合并报 -4016
	21. 卡合并，ob_micro_block_cache 模块报错：failed to allocate value, -4013
	22. 磁盘性能问题导致卡合并和磁盘写入拒绝
	23. OceanBase 数据库卡合并报 -4024
	24. 无法进行合并，错误代码 4217
	25. Compaction 报错 -4677
	26. 手动发起合并结果返回 -6210
	27. insert into ignore 执行卡住
	28. 合并报错 column checksum is not equal
	29. 合并报错，日志有 obj1 and obj2 can't compare 的错误
	30. 各个 Zone 合并完成变为 idle 状态后，集群的合并状态依然为 merging
	31. checksum error ret=-4103
	32. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/如何排查合并慢问题\|如何排查合并慢问题]]，如何排查合并慢问题
	33. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/合并慢怎么确定是否合理\|合并慢怎么确定是否合理]]，合并慢怎么确定是否合理？
	34. 为什么导数的时候 minor freeze 会越来越慢
	35. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/OceanBase 数据库合并超时问题排查方法\|OceanBase 数据库合并超时问题排查方法]]，OceanBase 数据库合并超时问题排查方法
	36. 租户 Locality 变更失败，合并超时
	37. OBServer major 合并超时
	38. 合并超时，错误代码 4023
	39. OceanBase 集群合并超时 Timeout 排查方法
	40. 磁盘故障导致的合并超时，SQL 执行超时
	41. 异地 Zone5 合并超时，Clog 不断重复 Rebuild 问题分析
	42. 合并期间空间膨胀问题
	43. enable_manual_merge 参数是否会影响 alter system major freeze 的执行结果
	44. 大分区合并写 slog 超限失败
	45. 集群合并卡住，合并过程中带宽被打满
	46. 合并切主失败问题排查


##### 1.9 存储管理 
1. 编码和压缩
2. OceanBase 数据库 V2.X 版本的缓存预热功能
3. [单个分区的宏块数限制说明](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209897?back=kb)，；
4. 如何修改 data 盘的占用大小
5. OceanBase 数据库 V4.2 数据文件按需扩展
6. [如何查看表和索引的大小和磁盘空间使用](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209900?back=kb)，；
7. [OceanBase 数据库中怎么查看临时文件的磁盘空间占用](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209899?back=kb)，；
8. slog 介绍
9. OceanBase 数据库如何配置日志自动清理？
10. 各组件日志清理策略
11. OceanBase 数据库 V2.x 版本中日志缺失的原因
12. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.9 存储管理/数据盘文件系统在线扩容方法\|数据盘文件系统在线扩容方法]]，；
13. OceanBase 数据库日志中出现 Fail to alloc data block, (ret=-4184)
14. OceanBase 数据库 V4.x 版本 RS 端合并卡住排查手册
15. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.9 存储管理/OBServer 服务器日志目录磁盘使用率超限报错 4264\|OBServer 服务器日志目录磁盘使用率超限报错 4264]]，OBServer 服务器日志目录磁盘使用率超限报错 4264，；
16. 日志报错 -4184: ChunkServer out of disk space
17. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.9 存储管理/OceanBase 数据库 V2.X 版本的磁盘故障检测原理\|OceanBase 数据库 V2.X 版本的磁盘故障检测原理]]，主要介绍 OceanBase 数据库的磁盘故障检测逻辑；
18. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.9 存储管理/OceanBase 数据库 V3.X 版本的磁盘故障检测原理\|OceanBase 数据库 V3.X 版本的磁盘故障检测原理]]，主要介绍 OceanBase 数据库的磁盘故障检测逻辑；
19. DTL 中间结果监控 dtl_interm_result_monitor
20. slog 升级前检查操作流程
21. 如何排查合并卡住问题
22. KVCache 排查手册
23. 关于 Cache 占用高的说明
24. 临时文件排查手册
25. 冻结与转储流程对应日志情况
26. DAG 报错 OB_SIZE_OVERFLOW(4019)/OB_EAGAIN(4023)

##### 1.10 内存管理
1. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.10 内存管理/OceanBase 数据库 MEMStore 内存占满问题的排查方法\|OceanBase 数据库 MEMStore 内存占满问题的排查方法]]，；
2. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.10 内存管理/MemStore 内存不足\|MemStore 内存不足]]，【未完成】；
3. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.10 内存管理/内存相关问题\|内存相关问题]]，；

##### 1.11 高可用
1. OceanBase 数据库高可用
2. `物理备库`

##### 1.12 安全和权限

##### 1.13 备份恢复

##### 1.14 性能诊断和优化
1. OceanBase 数据库预设资源监控点
2. OceanBase 数据库如何通过 TraceID 查到对应的 OBServer 主机
3. OceanBase 数据库日志中的 [lt=xxx] 和 [dc=xxx] 的含义是什么？
4. 如何使用 mysqlslap 在 OceanBase 数据库中模拟客户端高并发或快速构造测试数据
5. 如何配置集群的 CPU 并发数
6. 弱读从/只读副本遇到长事务切主场景下导致 CPU 使用率 100% 的原因
7. 如何判断 OBServer 是否存在 IO 限流?
8. GV$SYSSTAT 中租户 cpu_usage 的统计方法
9. IO hang 导致磁盘 invalid 和 stop_server
10. OBServer 升级 V3.2.3 版本后，持续报磁盘 IO 超时，但系统监控负载低
11. OBServer 并发线程数过多导致租户登录慢或 SQL 执行慢
12. SQL 诊断和优化
	1. OceanBase 数据库的 ACS 功能介绍
	2. DAS（data access service）简介  
	3. OceanBase 数据库中快速参数化的含义
	4. SQL 中表含 enum 列导致 LEFT JOIN 未 rewrite 为 JOIN
	5. 如何人工控制 CTE 的展开与物化？
	6. 如何快速获得表中某列的 NDV 值
	7. OceanBase 数据库 V4.x 版本中排查 IO 问题的方法以及 IO 相关的日志和视图
	8. NLJ 和 SPF 中 Rescan 的性能问题
	9. OceanBase 数据库中问题 SQL 限流到 0 后 CPU 使用率未降低
	10. SQL 执行报错 errcode=-5307
	11. Query Block 介绍
	12. UDF 的 DETERMINISTIC 属性对 SQL 性能的影响
	13. 优化器将 exists 改写成 semi-join 后变慢的原因？
	14. 子查询改写错误导致 SQL 查询结果为空的问题
	15. 优化器将 NOT EXISTS 改写成 Anti-Join 后变慢的原因？
	16. OceanBase 数据库中 EXISTS 的条件为单个常量导致执行慢的原因与解决方法
	17. OceanBase 数据库性能视图 gv$sql_audit 介绍
	18. 使用 use_concat hint 优化 or 语句的性能
	19. OceanBase 数据库的 SQL 审计信息淘汰机制
	20. SQL Audit 内存管理与淘汰机制
	21. 诊断 TEMP TABLE 抽取的性能问题
	22. 如何设置及查看 SQL 审计及执行计划
	23. gv$sql_audit 中一个 TRACE_ID 为什么对应多条记录
	24. 表 gv$sql_audit 漏写业务 SQL 的情况
	25. SQL 执行报错而不能计入 SQL_AUDIT 的情况说明
	26. [如何清理 SQL AUDIT](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000133?back=kb)，；
	27. 通过 SQL AUDIT 无法查询客户端 IP 地址
	28. 如何开启 OceanBase 数据库 MySQL 模式中的 SQL TRACE
	29. SQL_ID, Plan_ID 与 Plan_hash
	30. OceanBase 的 TRACE EVENT 是如何采集和实现的
	31. OceanBase 数据库的 sql_id 和 plan_id 的稳定性介绍
	32. NLJ/SPF batch rescan 问题汇总
	33. GV$PLAN_CACHE_PLAN_STAT 表中 executions=0 的原因
	34. 远程执行的 SQL 在 PLAN CACHE 中有 2 个执行计划
	35. 影响 plan_id 共享的 sys_vars 变量
	36. 统计信息时间查询
	37. ANALYZE TABLE 报错 -4016
	38. 如何查看 OceanBase 数据库所有租户中的查询计划？
	39. OceanBase 数据库如何固化视图中 SQL 的执行计划
	40. 如何通过 SQL_ID 查询实时执行计划
	41. 如何查看 SQL 执行的物理执行计划
	42. 如何使用 outline 绑定执行计划
	43. Oracle 租户下包含 rownum 时查看执行计划中相关的信息
	44. SELECT 投影列中关联子查询的 SQL 性能问题
	45. 如何导出 OUTLINE 实现稳定的执行计划
	46. 执行计划结果中 Optimization info 详解
	47. 执行计划绑定后，通过 EXPLAIN 语句发现执行计划没有按期望执行
	48. 多表连接生成计划报错 unexpected operator type
	49. 并行 DML(PDML) 性能不及预期的问题
	50. 开启 SPM 功能影响 DDL 执行或导致 schema 内存占用大或影响 OBServer 重启
	51. OceanBase 数据库 MySQL 模式手工收集统计信息的方法
	52. only px support this plan 报错处理方法
	53. 大查询线程的管理和调度机制
	54. 如何检查大查询整体情况
	55. 如何配置 OBProxy 捕获慢查询
	56. OBProxy 查询二级分区路由不准确
	57. 如何配置 OBServer 捕获慢查询
	58. OceanBase 数据库日志中 SLOW QUERY 的 process_end 事件耗时过长的原因
	59. 如何通过参数调整确认慢 SQL
	60. 数据倾斜导致的 Hash Join 性能问题
	61. 如何排查 PX 执行卡住
	62. OceanBase 数据库开启 NLJ BATCH 优化在 BATCH RESCAN 和 UNION 算子正交导致的执行报错 4016
	63. SQL 执行报错 -4119，RPC packet to send too long
	64. 优化器不使用索引首列的直方图信息进行估算
	65. MySQL 模式 Truncate/Drop 分区操作耗时很长的原因
	66. SQL 在数据库中的执行时间超过 OB_QUERY_TIMEOUT 的原因
	67. SQL 中使用 utl_raw.cast_to_raw 函数无法同时使用并行
	68. PX 并行度与速度快慢的关系
	69. PX 执行无法获得线程，无限重试直到 ob_query_timeout 超时
	70. OceanBase 数据库日志 ob_px_admission 报错 out of px worker resource
	71. 多条 SQL 执行使用相同的 trace_id 的原因
	72. Union 语句不支持使用 no_rewrite hint
	73. Hint 中 Table Name 的指定方法
	74. 快速获取 SQL 执行的 svr_ip、trace_id 的方法
	75. 使用 PARALLEL(n) Hint 实际使用的并行线程不符合预期
	76. [Queuing 表查询缓慢问题](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209901?back=kb)，；
	77. 数据删除后的空表查询耗时长
	78. 使用临时表导致系统登录慢的问题
	79. 判定大查询导致第二次执行慢的问题
	80. 匿名块 LOOP 调用硬解析导致业务批处理积压
	81. 执行 set ob_enable_trace_log = 1 不生效
	82. 使用 sql_plan_monitor 与 monitor dump 诊断正确性问题
	83. 开启表并行后，limit 失效，没有选择最优索引
	84. 并行查询执行性能异常的场景之一
	85. 应用 SQL 在 OceanBase 侧执行耗时抖动
	86. PDML SQL 性能问题排查
	87. 关于集群隐藏参数 \_enable_new_sql_nio 的说明
	88. Oracle 模式下开启 PS 和 cursor，SQL Audit 记录的 PARAMS_VALUE 是空的
	89. explain SQL 报错 4000
	90. OceanBase 数据库执行计划生成模块使用内存过多
	91. DBA_SCHEDULER_WINDOWS 视图的 NEXT_RUN_DATE 不符合预期
	92. 自动内存管理锁冲突问题
	93. PX+UDF 函数+索引，PX 和索引并发，直接报事务 6220 错误或在报事务 6220 之后因超时时间过长反复重试，执行时间过长
	94. 分区表 local 索引排序的性能问题
	95. OceanBase 数据库中索引查询效率低于 Oracle 的问题
	96. 通过 gv$sql_audit 视图统计某段时间内的读写流量
	97. gv$plan_cache_plan_stat 中命中次数大于执行次数原因
	98. 同一个 sql_id 有多个执行计划
	99. 同一条 SQL 使用不同的参数执行时快时慢
	100. Table 数据量相同的两个环境，相同执行计划的 SQL 执行性能差异较大的原因
	101. 日志告警 Transformer: iteration count overflow
	102. 读写冲突导致的性能问题
	103. SQL 执行中生成的临时文件的释放时间
	104. 主副本存在长事务时，从副本弱读 hung 的问题
	105. transfer 导致查询性能下降
13. OceanBase 数据库 V4.2 版本，关于锁冲突问题的排查手册



##### 1.15 SQL 和数据库对象
1. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.15 SQL 和数据库对象/如何查看当前的数据库与用户名\|如何查看当前的数据库与用户名]]，；
2. `数据类型和字符集相关问题`
3. `函数相关问题`
4. `DDL 和数据库对象`
5. `DML 操作相关问题`
	1. *查询相关问题*
	2. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.15 SQL 和数据库对象/OceanBase 数据库大批量删除数据最佳实践\|OceanBase 数据库大批量删除数据最佳实践]]，；
	3. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.15 SQL 和数据库对象/OceanBase 数据库 MySQL 模式执行 Delete 语句报 fatal internal error\|OceanBase 数据库 MySQL 模式执行 Delete 语句报 fatal internal error]]，；

##### 1.16 PL

##### 1.17 数据迁移

##### 1.18 其他
2. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.18 其他/创建表失败，错误代码 4624\|创建表失败，错误代码 4624]]，；
3. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.18 其他/创建索引直至超时失败\|创建索引直至超时失败]]，；
4. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.18 其他/Oracle 租户 GBK 字符集生僻字显示乱码\|Oracle 租户 GBK 字符集生僻字显示乱码]]，；
5. [[15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.18 其他/如何手动执行切主\|如何手动执行切主]]，；

#### 2 OMS 
1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 全量数据迁移报错\|OMS 全量数据迁移报错]]，错误信息：ORA-24816: Expanded non LONG bind data supplied after actual LONG or LOB column；


### 参考文档
1. [OceanBase 数据库知识导图](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000324072)，；
2. `错误信息概述`：[错误码_MySQL 模式](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000508246)，[错误码_Oracle 模式](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000508246)，；
