---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数详细介绍/","dgPassFrontmatter":true}
---


### OBProxy 配置参数详细介绍


#### 1 支持动态修改的参数

1. binlog_service_ip
	1. 用于设置 binlog 服务的 IP，格式为 *ip1:sql_port1*；
2. cache_cleaner_clean_interval	= 20s
	1. 默认值为：20s，取值范围：[1s, 1d]；cache cleaner 清理缓存的间隔时间；
3. check_tenant_locality_change ：用于控制是否启用位置变化触发位置缓存脏数据；
4. client_max_connections = 8192
	1. 默认值为：8192，取值范围：[0, +∞]；ODP 支持的最大客户端连接数；
5. client_max_memory_size：用于设置客户端会话的最大动态分配内存大小；
6. client_session_id_version
7. client_sock_option_flag_out = 2
	1. ODP 3.x 版本之前默认值为 2，自 3.x 版本起默认值修改为 3；取值范围：`[0, +∞)`；ODP 与客户端的 socket flag 参数；
	2. 主要控制 client -> ODP 连接的保活机制的开启，该参数设置为 2 时，默认为所有 client 侧的连接开启保活机制；
8. client_tcp_keepcnt = 5：设置持续无 ack 的保活报文数量最大阈值；
	1. 默认值为：5，取值范围：[0, 9]；ODP 与客户端连接的 TCP 存活 probe count；
	2. 当 client -> ODP 连接的保活机制开启后，保活报文需要被 ack，如果保活报文没有被 ack，则说明连接存在问题，TCP会在强制断连之前，记录持续无 ack 的保活报文数量，当持续无 ack 的数量达到该阈值时强制断连；
9. client_tcp_keepidle = 5
	1. 默认值为：5，取值范围：[0, 7200]；ODP 与客户端连接的 TCP 存活空闲时间；
	2. 控制当 client -> ODP 连接的保活机制开启后，持续多少时间无数据包传输开始发送保活报文；
10. client_tcp_keepintvl = 5		
	1. 默认值为：5，取值范围：[0, 75]；ODP 与客户端连接的 TCP 存活时间间隔；
11. client_tcp_user_timeout = 0
	1. 默认值为：0，取值范围：[0, 20]；ODP 与客户端连接的 TCP 用户超时时间；
12. cluster_count_high_water_mark = 256
	1. 默认值为：256，取值范围：[2, 102400]；允许创建的最大集群资源；
13. cluster_expire_time = 1d	
	1. 默认值为：1d，取值范围：`[0, +∞)`；集群元数据空闲超时时间；
14. compression_algorithm
15. config_server_refresh_interval = 60s
	1. 默认值为：60s，取值范围：[10s, 1d]；刷新 config server 的时间间隔；
16. congestion_fail_window = 120s
	1. 默认值为：120s，取值范围：[1s, 1h]；OceanBase 数据库服务器出错统计周期，与congestion_failure_threshold 配合使用；
17. congestion_failure_threshold	= 5
	1. 默认值为：3，取值范围：`[0, +∞)`；
	2. 判断是否将 OceanBase 数据库服务器列入黑名单。在congestion_fail_window 周期内，当 OceanBase 数据库服务器出错次数超过该值时，ODP 将这台服务器加入黑名单；
18. congestion_retry_interval = 20s		
	1. 默认值为：20s，取值范围：[1s, 1h]；存活却不可用黑名单中 OceanBase 数据库服务器的重试时间间隔；
19. connection_diagnosis_option
20. connect_observer_max_retries = 3
	1. 默认值为：3，取值范围：[2, 5]；ODP 重连 OceanBase 数据库的最大次数；
21. current_local_config_version
22. default_buffer_water_mark = 32KB
	1. 默认值为：32KB，取值范围：[4B, 64KB]；默认的 buffer water mark；
23. default_inactivity_timeout = 180000s
	1. 默认值为：180000s，取值范围：[1s, 30d]；TCP 连接的默认超时时间；
24. delay_exit_time = 100ms
	1. 默认值为：100ms，取值范围：[100ms, 500ms]；ODP 延迟退出的时间；
25. delay_update_entry_interval
26. detect_server_timeout
27. digest_sql_length
28. enable_abort_conn_info
29. enable_async_log = true	
	1. 默认值为：true；判断是否开启异步日志；
30. enable_async_pull_location_cache
31. enable_bad_route_reject	= false
	1. 默认值为：false；判断开启黑名单时，是否拒绝无法路由的请求；
32. enable_binlog_service
33. enable_cached_server
34. enable_causal_order_read
35. enable_client_connection_lru_disconnect
36. enable_client_ip_checkout = true
	1. 默认值为：true；判断是否校验客户端 IP；
37. enable_client_ssl
38. enable_cloud_full_username
39. enable_cluster_checkout
	1. 默认值为：true	；判断是否校验集群名称；
40. enable_congestion = true
	1. 默认值为：true；判断是否开启黑名单；
41. enable_connection_diagnosis
42. enable_extra_prometheus_metric
43. enable_flow_control = true
	1. 默认值为：true；判断是否在 tunnel 中开启流量控制；
44. enable_full_username
45. enable_get_rslist_remote
46. enable_index_route
47. enable_monitor_stat
48. enable_ob_protocol_v2_with_client
49. enable_partition_table_route = true
	1. 默认值为：3；判断是否开启 partition table 路由；
50. enable_performance_mode
51. enable_pl_route = true
	1. 默认值为：true；判断否开启过程化程序语言（PL）路由；
52. `enable_primary_zone = True`
	1. 用于控制 ODP 计算路由失败情况下是否根据租户 Primary Zone 优先级进行路由；
	2.  默认值为：True；True，根据租户 Primary Zone 优先级进行路由；False，路由失败的情况下进行随机路由；
53. enable_prometheus
54. enable_proxy_scramble
55. enable_qa_mode
56. enable_qos
57. enable_read_write_split
58. enable_report_session_stats = false
	 1. 默认值为：false；判断是否开启 session 级别统计项汇报到内部表；
59. enable_reroute = false
	1. 默认值为：false；判断是否开启二次路由；
60. enable_sequence_prefetch
61. enable_server_ssl
62. enable_standby
63. enable_stat
64. enable_strict_stat_time = true
	1. 默认值为：true；判断是否开启 strict statistic time；
65. enable_sync_all_stats = true
	1. 默认值为：true	；判断是否开启同步所有统计；
66. enable_syslog_file_compress
67. enable_syslog_wf
68. enable_trace
69. enable_trace_stats = false
	 1. 默认值为：false；判断是否开启 MySQL 追踪统计；
70. enable_trans_detail_stats = true
	1. 默认值为：true；判断是否开启事务状态统计；
71. enable_transaction_internal_routing
72. enable_transaction_split
73. enable_weak_reroute
74. enable_xa_route
75. fetch_proxy_bin_random_time = 300s
	1. 默认值为：300s，取值范围：[1s, 1h]；ODP 执行热升级时，在获取新二进制文件之前需要等待的最长随机时间；
76. fetch_proxy_bin_timeout = 120s
	1. 默认值为：120s，取值范围：[1s, 1200s]；热升级获取新二进制文件的超时时间；
77. flow_consumer_reenable_threshold = 256		
	1. 默认值为：256，取值范围：[0, 131072]；tunne 流量控制配置数据转发次数阈值；
78. flow_event_queue_threshold = 5
	1. 默认值为：5，取值范围：[0, 20]；tunnel 流量控制配置工作线程队列中任务数阈值；
79. flow_high_water_mark = 64k		
	1. 默认值为：64k，取值范围：[0, 16MB]；tunnel 中流量控制的上限；
80. flow_low_water_mark = 64k		
	1. 默认值为：64k，取值范围：[0, 16MB]；tunnel 中流量控制的下限；
81. hot_upgrade_exit_timeout
82. hot_upgrade_failure_retries = 5
	1. 默认值为：5，取值范围：[1, 20]；热升级失败后重试次数；
83. hot_upgrade_rollback_timeout = 24h
	1. 默认值为：24h，取值范围：[1s, 30d]；热升级之后, 执行回滚的默认等待时间；
84. idc_list_refresh_interval = 2h
	1. 默认值为：2h，取值范围：[10s, 1d]；刷新 IDC 信息的时间间隔；
85. ignore_local_config = true
	1. 默认值为：true；判断是否忽略本地配置文件；
86. internal_cmd_mem_limited
87. ldg_info_refresh_interval
88. log_cleanup_interval = 1m
	1. 默认值为：1m，取值范围：[5s, 30d]；执行清理任务的时间间隔；
89. log_dir_size_threshold = 64GB
	1. 默认值为：64GB，取值范围：[256MB, 1T]；ODP 日志大小阈值。超过阈值即进行日志清理；
90. log_file_percentage = 80
	1. 默认值为：80，取值范围：[0, 100]；ODP 日志百分比阈值。超过阈值即进行日志清理；
91. long_async_task_timeout = 60s
	1. 默认值为：60s，取值范围：[1s, 1h]；长异步任务超时时间；
92. max_log_file_size = 256MB
	1. 默认值为：256MB，取值范围：[1MB, 1G]；单个日志文件的最大尺寸；
1. max_syslog_file_count
3. max_syslog_file_time
4. mem_leak_check_class_name
5. mem_leak_check_mod_name
6. metadb_server_state_refresh_interval = 60s
	1. 默认值为：60s，取值范围：[10ms, 1h]；刷新 MetaDB 的时间间隔；
7. min_congested_connect_timeout = 100ms
	1. 默认值为：100ms，取值范围：[1ms, 1h]；存活却不可用的最小超时时间；
8. min_keep_congestion_interval = 20s
	1. 默认值为：20s，取值范围：[1s, 1d]；黑名单中的 OceanBase 数据库服务器在洗白之前需要等待的最短时间；
9. monitor_cost_ms_unit
10. monitor_item_limit
11. monitor_item_max_idle_period
12. `monitor_log_level = INFO`
	1. 默认值为：INFO，取值范围：DEBUG, TRACE, INFO, WARN, USER_ERR, ERROR；监控日志级别；
13. monitor_stat_dump_interval
14. monitor_stat_high_threshold
15. monitor_stat_low_threshold
16. monitor_stat_middle_threshold
17. mysql_version
18. need_convert_vip_to_tname
19. net_config_poll_timeout	= 1ms
	1. 默认值为：1ms，取值范围：`[0, +∞)`；网络事件超时时间；
20. normal_pl_update_threshold
21. ob_max_read_stale_time
22. obproxy_force_parallel_query_dop
23. obproxy_read_consistency
24. obproxy_read_only
25. obproxy_sys_password
26. observer_query_timeout_delta = 20s
	1. 默认值为：20s，取值范围：[1s, 30s]；OceanBase 数据库进程的 ob_query_timeout 增量值；
27. observer_sys_password
28. observer_sys_password1
29. partition_location_expire_relative_time
30. prometheus_sync_interval
31. protocol_diagnosis_level
32. proxy_hot_upgrade_check_interval
33. proxy_id
34. proxy_idc_name
35. proxy_info_check_interval = 60s
	1. 默认值为：60s，取值范围：[1s, 1h]；ODP 检测配置更新的间隔时间；
36. proxy_local_cmd
37. proxy_mem_limited = 2G		
	1. 默认值为：2G，取值范围：[100MB, 100G]；ODP 运行时内存上限。超过上限 ODP 即自动退出；
38. proxy_primary_zone_name 
39. proxy_route_policy
40. proxy_tenant_name
41. qa_mode_mock_public_cloud_slb_addr
42. qos_stat_clean_interval
43. qos_stat_item_limit
44. query_digest_time_threshold = 100ms		
	1. 默认值为：100ms，取值范围：[0s, 30d]；请求执行时间阈值，超过阈值即打印一条日志到 obproxy_digest.log 文件；
45. read_stale_retry_interval
46. request_buffer_length = 4KB		
	1. 默认值为：4KB，取值范围：[1KB, 16MB]；请求 buffer 大小；
47. route_diagnosis_level
48. routing_cache_mem_limited = 128M		
	1. 默认值为：128M，取值范围：[1KB, 100G]；PL/SQL 路由缓存内存上限；
49. sequence_entry_expire_time
50. sequence_fail_retry_count
51. sequence_prefetch_threshold
52. server_detect_fail_threshold
53. server_detect_mode
54. server_detect_refresh_interval
55. server_routing_mode
56. `server_sock_option_flag_out`
	1. 主要控制 ODP -> OBServer 节点连接的保活机制的开启，该参数设置为 2 时，默认为所有 server 侧的连接开启保活机制；
57. server_state_refresh_interval	= 20s
	1. 默认值为：20s，取值范围：(10ms, 1h]；刷新 OceanBase 数据库进程状态的时间间隔；
58. server_tcp_init_cwnd = 0	
	1. 默认值为：0，取值范围：[0, 64]；新建 Server 连接时设置 TCP 初始拥塞窗口值；
59. server_tcp_keepcnt
60. server_tcp_keepidle = 5
	1. 默认值为：5，取值范围：[0, 7200]；ODP 与 OceanBase 数据库连接的 TCP 存活空闲时间；
	2. 控制当 ODP -> OBServer 节点连接的保活机制开启后，持续多少时间无数据包传输开始发送保活报文；
61. server_tcp_keepintvl = 5
	1. 默认值为：5，取值范围：[0, 75]；ODP 与 OceanBase 数据库连接的 TCP 存活时间间隔；
62. server_tcp_user_timeout = 0
	1. 默认值为：0，取值范围：[0, 20]；ODP 与 OceanBase 数据库连接的 TCP 用户超时时间；
63. short_async_task_timeout = 5s		
	1. 默认值为：5s，取值范围：[1s, 1h]；短异步任务超时时间；
64. skip_proxy_sys_private_check
65. skip_proxyro_check
66. slow_proxy_process_time_threshold = 2ms
	1. 默认值为：2ms，取值范围：[0s, 30d]；ODP 预执行请求执行时间阈值。超过阈值即打印一条告警日志；
67. slow_query_time_threshold = 500ms
	1. 默认值为：500ms，取值范围：[0s, 30d]；慢请求执行时间阈值。超过阈值即打印一条日志到 obproxy_slow.log 文件；
68. slow_transaction_time_threshold = 1s
	1. 默认值为：1s，取值范围：[0s, 30d]；事务执行时间阈值。超过阈值即打印一条告警日志；
69. sock_option_flag_out = 3
	1. 默认值为：3，取值范围：[0, +∞]；socket flag 参数；
70. sock_packet_mark_out = 0
	1. 默认值为：0，取值范围：[0, +∞]；socket mark 参数
71. sock_packet_tos_out = 0		
	1. 默认值为：0，取值范围：[0, +∞]；socket tos 参数；
72. sock_recv_buffer_size_out = 0
	1. 默认值为：0，取值范围：[0, 8MB]；socket 的接收缓冲区大小；
73. sock_send_buffer_size_out = 0
	1. 默认值为：0，取值范围：[0, 8MB]；socket 的发送缓冲区大小；
74. sql_table_cache_expire_relative_time
75. sql_table_cache_mem_limited
76. sqlaudit_mem_limited
77. ssl_attributes
78. stat_dump_interval	= 6000s
	1. 默认值为：6000s，取值范围：[0s, 1d]；转储统计全局统计信息任务的时间间隔；
79. stat_table_sync_interval = 60s 
	1. 默认值为：60s，取值范围：[0s,1d]；更新内部表全局统计项任务的时间间隔；
80. syslog_io_bandwidth_limit
81. syslog_level = INFO
	1. 默认值为：INFO，取值范围：DEBUG, TRACE, INFO, WARN, USER_ERR, ERROR；日志级别；
82. target_db_server
83. tenant_location_valid_time
84. tunnel_request_size_threshold = 8KB		
	1. 默认值为：8KB，取值范围：(0, 16MB]；
	2. ODP 接收请求次数的阈值。当请求次数超过该阈值时, ODP 直接使用 tunnel 转发客户端请求, 且不解析请求；
85. username_separator
86. xflush_log_level = INFO
	1. 默认值为：INFO，取值范围：DEBUG, TRACE, INFO, WARN, USER_ERR, ERROR；xflush 日志级别；


#### 3 不支持动态修改的参数
1. stack_size = 1MB，	线程栈大小。用于创建线程；
	1. 默认值为：1MB，取值范围：[1MB,10MB]；
2. `work_thread_num = 128`：工作线程数。用于初始化工作线程；
	1. 默认值为：128，取值范围：[1,128]；
3. task_thread_num = 2，任务线程数。用于初始化任务线程；
	1. 默认值为：2，取值范围：[1,4]；
4. block_thread_num，阻塞型任务线程数，用于初始化线程
	1. 默认值为：1，取值范围：[1,4]；
5. grpc_thread_num，grpc 线程数；
	1. 默认值为：8，取值范围：[8,16]；
6. grpc_client_num，grpc 客户端数；
	1. 默认值为：9，取值范围：[9,16]；
7. automatic_match_work_thread = true
	1. 默认值为：true	；判断是否根据 CPU 核数自动创建工作线程。如果该选项为 true，上限为 work_thread_num
8. enable_strict_kernel_release = true，判断是否需要校验 OS kernel；
	1. 默认值为：true；
9. net_accept_threads	= 2，执行 accept 的线程数；
	1. 默认值为：2，取值范围：[0,8]；
10. frequent_accept = true
	1. 默认值为：true；初始化 net accept 参数；
11. enable_cpu_topology = false
	1. 默认值为：false；判断是否开启 CPU 亲和；
12. enable_compression_protocol = true
	1. 默认值为：true；判断是否开启压缩协议；
13. enable_ob_protocol_v2 = true
	1. 默认值为：true；是否开启 OB2.0 协议；


#### 4 内存级参数

1. refresh_json_config = false	
	1. 该参数默认值为 false，重新从 config server 获取 json 配置；
2. refresh_rslist = false	
	1. 该参数默认值为 false，重新从 config server 获取 rslist 配置；
3. refresh_idc_list = false		
	1. 该参数默认值为 false，重新从 config server 获取 idc 配置；
4. refresh_config	= false	
	1. 该参数默认值为 false，重新从 MetaDB 更新所有配置；


### 参考文档：
1. [支持动态修改的参数_V4.2.1](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050267)，[不支持动态修改的参数_V4.2.1](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050265)，[内存级参数_V4.2.1](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050268)，；

