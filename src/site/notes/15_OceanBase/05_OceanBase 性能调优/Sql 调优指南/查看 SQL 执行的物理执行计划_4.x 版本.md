---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查看 SQL 执行的物理执行计划_4.x 版本/","dgPassFrontmatter":true}
---


### 查看 SQL 执行的物理执行计划

#### 1 查看 SQL 执行的物理执行计划
OceanBase 数据库每个服务器的计划缓存都是独立的；

##### 1.1 查询  IP、Port、tenant_id，plan_id
查询 sql 实际执行计划必须给定 IP、Port、`tenant_id` 和 `plan_id` 这四列的值，可以通过下面的方式获取到该信息；

###### 1.1.1 gv$ob_sql_audit 
关于 `gv$ob_sql_audit` 视图更多信息，请参考：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 gv, ob_sql_audit 视图_4.x\|查询 gv, ob_sql_audit 视图_4.x]]，；

```sql
-- 1.查询执行计划信息
	-- 查询 sql 实际执行计划必须给定 IP、Port、tenant_id 和 plan_id 这四列的值；
	-- 除了可以通过下面的方式获取到该信息，还可以通过其他方式获得该信息； 
	SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, type, elapsed_time, execute_time, query_sql FROM oceanbase.gv$sql_audit 
	WHERE tenant_id=1001 and user_name='xxxx' and query_sql like'%'； 
	select * from oceanbase.gv$ob_sql_audit where sql_id='ACC13731E9CEC5DD5BE06DE726E9DE6D' limit 5; 
	select /*+ QUERY_TIMEOUT(10000000) */ * from oceanbase.v$ob_sql_audit where SQL_ID='EEE062DD3082DFDB90B4F454F95081B8' limit 5\G
		-- sql id 可以通过 OCP SQL 诊断模块获得；
```

###### 1.1.2 gv$plan_cache_plan_stat
从 V4.0.0 版本开始视图名由 `GV$PLAN_CACHE_PLAN_STAT` 调整为 `GV$OB_PLAN_CACHE_PLAN_STAT`；该视图记录计划缓存中所有执行计划的具体信息及每个计划总的执行统计信息；该视图详细情况：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/计划缓存相关的视图\|计划缓存相关的视图]]，；

```sql
-- 1.登入租户
	obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p    -- 登录业务 MySql 租户
	obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p   -- 通过普通用户登录 Oracle 租户

-- 2.
	-- 使用 root@sys 登录 OceanBase 数据库的 sys 租户  
	
	-- 查看当前集群中的租户信息  
	SELECT tenant_id,tenant_name FROM oceanbase.gv$tenant;  
	
	-- 使用 root@sys 登录 OceanBase 数据库的 sys 租户  
	SELECT tenant_id, svr_ip, svr_port, sql_id, plan_id, last_active_time, first_load_time, outline_data
	FROM oceanbase.GV$OB_PLAN_CACHE_PLAN_STAT
	WHERE TENANT_ID = 1002 AND SQL_ID = '3310A1D1D81D4BA92CEEF42538136DD1' AND SVR_IP = '11.xxx.x.xx' AND SVR_PORT = 35046;

	SELECT svr_ip,svr_port, plan_Id,TYPE,d.database_name, query_sql, first_load_time, avg_exe_usec, slow_count,executions, slowest_exe_usec, sql_id  
	FROM oceanbase.gv$ob_plan_cache_plan_stat s  
	JOIN oceanbase.gv$database d ON (s.tenant_id=d.tenant_id AND s.db_id=d.database_id)  
	WHERE s.tenant_id=1008 AND d.database_name LIKE 'test%' AND query_sql LIKE '%tab_1%'  
	ORDER BY avg_exe_usec desc;  

-- 3.通过 plan_id 或 SQL 语句 查询信息
	SELECT * FROM oceanbase.gv$ob_plan_cache_plan_stat WHERE plan_id = 1093;
	SELECT * FROM oceanbase.gv$ob_plan_cache_plan_stat WHERE query_sql LIKE '%INSERT INTO test%';  -- 通过 SQL
	SELECT * FROM oceanbase.gv$ob_plan_cache_plan_stat WHERE tenant_id= 1001 AND STATEMENT LIKE 'insert into tab_1%'  limit 10\G 
	SELECT /*+ QUERY_TIMEOUT(10000000) */ * FROM oceanbase.gv$ob_plan_cache_plan_stat WHERE tenant_id= 1008 and statement LIKE '%tab_1%' limit 10\G
	/* 字段介绍：
	tenant_id：；
	svr_ip：；
	svr_port：；
	plan_id：；
	sql_id：；
	type：remote 计划；
	statement：参数化 SQL；
	query_sql：；
	special_params：；
	sys_vars：第一次优化的查询；
	plan_hash：；
	first_load_time：；
	schema_version：环境参数；
	merged_version：；
	last_active_time：；
	avg_exe_usec：；
	slowest_exe_time：；
	slowest_exe_usec：；
	slow_count：；
	hit_count：；
	plan_size：；
	*/
```


##### 1.2 查询 sql 语句的实际执行计划
从 V4.0.0 版本开始引入 *gv$ob_plan_cache_plan_explain* 视图查询实际执行计划；该视图详细情况：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/计划缓存相关的视图\|计划缓存相关的视图]]，；

> [!NOTE] 注意：
> 如果访问 `v$ob_plan_cache_plan_explain`，必须给定 *tenant_id* 和 *plan_id* 的值，否则系统将返回空集；
> 如果访问 `gv$ob_plan_cache_plan_explain`，必须给定 *IP*、*Port*、*tenant_id* 和 *plan_id* 这四列的值，否则结果显示为空；


```sql
-- 2.查询 sql 语句的实际执行计划_V4.x 之后版本
select * from oceanbase.gv$ob_plan_cache_plan_explain where tenant_id=1 and svr_ip='xx.xx.xx.xx' and svr_port=2882 and plan_id=349;
```


### 参考文档
1. [实时执行计划展示](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357003)，；
2. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/gv, sql_audit 视图\|gv, sql_audit 视图]]，；
