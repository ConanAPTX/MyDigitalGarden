---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/gv, plan_cache_plan_explain/","dgPassFrontmatter":true}
---


### gv\$plan_cache_plan_explain，GV\$OB_PLAN_CACHE_PLAN_STAT
#### 1 功能
1. `V2.x，V3.x 版本`：
	1. *V$PLAN_CACHE_PLAN_STAT* 视图记录了当前租户在当前 Server 上的计划缓存中缓存的每一个缓存对象的状态；
	2. *GV$PLAN_CACHE_PLAN_STAT* 视图记录了当前租户在所有 Server 上的计划缓存中缓存的每一个缓存对象的状态；
3. `V4.x 及以后版本`
	1. *V$OB_PLAN_CACHE_PLAN_STAT* 展示当前租户在当前 OBServer 节点上的计划缓存中缓存的每一个缓存对象的状态；
	2. *GV$OB_PLAN_CACHE_PLAN_STAT* 视图记录 Plan Cache 中计划的详细信息及其执行统计信息，每个计划都一条记录；

> [!NOTE] 修改记录：
> 该视图从 V4.0.0 版本开始视图名由 `V$PLAN_CACHE_PLAN_STAT` 调整为 `V$OB_PLAN_CACHE_PLAN_STAT`；

#### 2 相关视图
_(g)v$plan_cache_plan_explain_：；  
_(G)V$OB_PLAN_CACHE_PLAN_STAT_：；

#### 3 常用查询

```sql
-- 查询 sql 语句的实际执行计划
-- 1. V2.x，V3.x 版本
	-- 若访问 gv$plan_cache_plan_explain，必须给定 IP、Port、tenant_id 和 plan_id 这四列的值；  
	SELECT * FROM oceanbase.v$plan_cache_plan_explain  WHERE tenant_id = 1008 AND plan_id = 12568;  
	  
	-- 如果访问 v$plan_cache_plan_explain，必须给定 tenant_id 和 plan_id 的值，否则系统将返回空集；  
	SELECT * FROM oceanbase.gv$plan_cache_plan_explain  WHERE IP='100.64.174.102' and Port=2882 and tenant_id = 1008 AND plan_id = 12568;  
	SELECT * FROM oceanbase.gv$plan_cache_plan_explain  WHERE IP=host_ip() AND PORT = rpc_port() and tenant_id = 1008 AND plan_id = 12568;

-- 2. V4.x 之后版本
	select * from GV$OB_PLAN_CACHE_PLAN_EXPLAIN where tenant_id=1 and svr_ip='xx.xx.xx.xx' and svr_port=2882 and plan_id=349;
```

通过 `gv$plan_cache_plan_stat` 查询信息，2.x，3.x 版本；

```sql
-- 1.登入租户
	obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p    -- 登录业务 MySql 租户
	obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p   -- 通过普通用户登录 Oracle 租户

-- 2.
	-- 使用 root@sys 登录 OceanBase 数据库的 sys 租户  
	
	-- 查看当前集群中的租户信息  
	SELECT tenant_id,tenant_name FROM oceanbase.gv$tenant;  
	
	-- 使用 root@sys 登录 OceanBase 数据库的 sys 租户  
	SELECT svr_ip,svr_port, plan_Id,TYPE,d.database_name, query_sql, first_load_time, avg_exe_usec, slow_count,executions, slowest_exe_usec, sql_id  
	FROM oceanbase.gv$plan_cache_plan_stat s  
	JOIN oceanbase.gv$database d ON (s.tenant_id=d.tenant_id AND s.db_id=d.database_id)  
	WHERE s.tenant_id=1008 AND d.database_name LIKE 'test%' AND query_sql LIKE '%tab_1%'  
	ORDER BY avg_exe_usec desc;  

-- 3.通过 plan_id 或 SQL语句 查询信息
SELECT * FROM oceanbase.gv$plan_cache_plan_stat WHERE plan_id = 1093;
SELECT * FROM oceanbase.gv$plan_cache_plan_stat WHERE query_sql LIKE '%INSERT INTO test%';  -- 通过 SQL
SELECT * FROM oceanbase.gv$plan_cache_plan_stat WHERE tenant_id= 1001 AND STATEMENT LIKE 'insert into tab_1%'  limit 10\G 
SELECT /*+ QUERY_TIMEOUT(10000000) */ * FROM oceanbase.gv$plan_cache_plan_stat WHERE tenant_id= 1008 and statement LIKE '%tab_1%' limit 10\G
```


#### 4 字段说明
1. `GV$OB_PLAN_CACHE_PLAN_STAT` 表字段：仅部分重要字段；
	1. `TENANT_ID`：租户 ID；
	2. `SVR_IP`：节点 IP，即服务器的 IP 地址；
	3. `SVR_PORT`：NUMBER(38)，NO，机器端口号；
	4. `PLAN_ID`：执行计划 ID，对应 *GV\$OB_SQL_AUDIT* 视图中的 *PLAN_ID* 字段；
	5. `SQL_ID`：SQL ID，对应 *GV\$OB_SQL_AUDIT* 视图中的 *SQL_ID* 字段；
	6. `TYPE`：执行计划的类型，*1* 表示本地计划、*2* 表示远程计划、*3* 表示分布式计划；
	7. `STATEMENT`：参数化后的 SQL 语句；
	8. `QUERY_SQL`：第一次加载计划时的原始 SQL 语句；
	9. `FIRST_LOAD_TIME`：第一次被加载时间；
	10. `SCHEMA_VERSION`：Schema 版本号；
	11. `LAST_ACTIVE_TIME`：最近一次被执行时间；
	12. `AVG_EXE_USEC`：平均执行时间；
	13. `SLOWEST_EXE_TIME`：最慢执行的时间戳；
	14. `SLOWEST_EXE_USEC`：最慢执行的耗时；
	15. `SLOW_COUNT`：判断为慢查询次数，阈值通过集群配置项 *trace_log_slow_query_watermark* 控制；
	16. `HIT_COUNT`：命中次数；
	17. `LARGE_QUERYS`：判断为大查询次数，阈值通过集群配置项 *large_query_threshold* 控制；
	18. `DELAYED_LARGE_QUERYS`：被判断为大查询且被丢入大查询队列的次数；
	19. `TIMEOUT_COUNT`：超时次数；
	20. `EXECUTIONS`：执行次数；
	21. `DISK_READS`：所有执行物理读次数；
	22. `DIRECT_WRITES`：所有执行写盘的次数；
	23. `BUFFER_GETS`：所有执行逻辑读次数；
	24. `APPLICATION_WAIT_TIME`：执行所有 application 类事件的总时间；
	25. `CONCURRENCY_WAIT_TIME`：执行所有 concurrency 类事件的总时间；、
	26. `USER_IO_WAIT_TIME`：所有执行所有 user_io 类事件的总时间；
	27. `ROWS_PROCESSED`：所有执行选择的结果行数或执行更改表中的行数；
	28. `ELAPSED_TIME`：所有执行接收到请求到执行结束消耗时间；
	29. `CPU_TIME`：所有执行消耗的 CPU 时间；
	30. `OUTLINE_ID`：是否命中用户创建的 Outline ID，为 *-1* 表示不是通过绑定 Outline 生成的计划；
	31. `OUTLINE_DATA`：计划对应的 Outline 信息；
	32. `TABLE_SCAN`：该查询是否为主键扫描；
	33. `EVOLUTION`：该执行计划是否在演进中；
	34. `EVO_EXECUTIONS`：演进次数；
	35. `PS_STMT_ID`：Prepare Statement 的 ID；



### 参考文档
1. `GV$PLAN_CACHE_PLAN_STAT`：[Oracle_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356201)，[MySql_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356324)，；
2. `GV$OB_PLAN_CACHE_PLAN_STAT`：[SYS_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219745)，[Oracle_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219080)，[MySql_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219910)，；
