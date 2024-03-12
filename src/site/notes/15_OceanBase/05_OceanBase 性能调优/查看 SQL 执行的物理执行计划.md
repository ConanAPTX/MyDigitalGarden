---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/查看 SQL 执行的物理执行计划/","dgPassFrontmatter":true}
---


### 1 查看 SQL 执行的物理执行计划

#### 1.1 获取实际执行计划
查询 sql 实际执行计划必须给定 IP、Port、`tenant_id` 和 `plan_id` 这四列的值，可以通过下面的方式获取到该信息；

关于 `gv$sql_audit` 视图更多信息，请参考：[[15_OceanBase/99_内部表介绍/gv, sql_audit 视图#3 查询 gv$sql_audit 视图\|gv, sql_audit 视图#3 查询 gv$sql_audit 视图]]，；还可以通过 `gv$plan_cache_plan_stat` 查询该信息，详情请参考：[[15_OceanBase/99_内部表介绍/gv, plan_cache_plan_explain\|gv, plan_cache_plan_explain]]，；若在 4.x 版本中请参考：[执行计划](https://www.oceanbase.com/docs/common-oceanbase-database-1000000000033850)，；

利用上面步骤获取的信息，使用以下语句查询实际执行计划，*gv$plan_cache_plan_explain* 视图详细情况可以查看：[[15_OceanBase/99_内部表介绍/gv, plan_cache_plan_explain\|gv, plan_cache_plan_explain]]，；

```sql
-- 1.查询执行计划信息
-- 查询 sql 实际执行计划必须给定 IP、Port、tenant_id 和 plan_id 这四列的值；
-- 除了可以通过下面的方式获取到该信息，还可以通过其他方式获得该信息； 
SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, type, elapsed_time, execute_time, query_sql FROM oceanbase.gv$sql_audit 
WHERE tenant_id=1001 and user_name='xxxx' and query_sql like'%'； 
select * from oceanbase.gv$sql_audit where sql_id='ACC13731E9CEC5DD5BE06DE726E9DE6D' limit 5; 
select /*+ QUERY_TIMEOUT(10000000) */ * from oceanbase.v$sql_audit where SQL_ID='EEE062DD3082DFDB90B4F454F95081B8' limit 5\G
	-- sql id 可以通过 OCP SQL 诊断模块获得；

-- 2.查询 sql 语句的实际执行计划
-- 2.1 V2.x，V3.x 版本
	-- 若访问 gv$plan_cache_plan_explain，必须给定 IP、Port、tenant_id 和 plan_id 这四列的值；  
	SELECT * FROM oceanbase.v$plan_cache_plan_explain  WHERE tenant_id = 1008 AND plan_id = 12568;  
	  
	-- 如果访问 v$plan_cache_plan_explain，必须给定 tenant_id 和 plan_id 的值，否则系统将返回空集；  
	SELECT * FROM oceanbase.gv$plan_cache_plan_explain  WHERE IP='100.64.174.102' and Port=2882 and tenant_id = 1008 AND plan_id = 12568;  
	SELECT * FROM oceanbase.gv$plan_cache_plan_explain  WHERE IP=host_ip() AND PORT = rpc_port() and tenant_id = 1008 AND plan_id = 12568;

-- 2.2 V4.x 之后版本
	select * from GV$OB_PLAN_CACHE_PLAN_EXPLAIN where tenant_id=1 and svr_ip='xx.xx.xx.xx' and svr_port=2882 and plan_id=349;
```

> [!NOTE] 注意：
> 如果访问 `v$plan_cache_plan_explain`，必须给定 *tenant_id* 和 *plan_id* 的值，否则系统将返回空集；
> 如果访问 `gv$plan_cache_plan_explain`，必须给定 *IP*、*Port*、*tenant_id* 和 *plan_id* 这四列的值，否则结果显示为空；


### 2 清理 PLAN CACHE 
#### 2.1  MySql 模式
```sql
-- 通过 root 用户登录 MySql 租户
obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p

-- 通过 root 用户登录到 sys 租户，执行 Plan cache 清空
alter system flush plan cache;                            -- 清空本机
alter system flush plan cache global;                     -- 清空整个集群
alter system flush plan cache tenant=ob_oracle global;    -- 清空指定租户
alter system flush plan cache sql_id='xxxx' global;       -- 清空指定 sqlid 
ALTER SYSTEM FLUSH PLAN CACHE sql_id='2DE6ADBEBA9523DC4D9D3C45D7B9A046' GLOBAL;  -- 根据 sql_id 清理 PLAN CACHE
alter system flush plan cache databases='db1,db2' global; -- 清空指定 DB
```


#### 2.2 Oracle  模式
```sql
-- 通过普通用户登录 Oracle 租户
obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p

-- 根据 sql_id 清理 PLAN CACHE
BEGIN
  dbms_plan_cache.purge(sql_id => '5F0B1122EA84DC6CF43D6F39131D11AA',schema => 'ALVIN',global => true);
END;
```


### 3 参考文档
1. [实时执行计划展示](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357003)，；


