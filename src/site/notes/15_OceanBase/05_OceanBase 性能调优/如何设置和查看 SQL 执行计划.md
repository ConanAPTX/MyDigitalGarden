---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/如何设置和查看 SQL 执行计划/","dgPassFrontmatter":true}
---


## 如何设置和查看 SQL 执行计划
### 1 开启 SQL 执行计划
设置变量 ob_enable_trace_log / ob_enable_plan_cache；
`注意`：建议开启 plan cache 之前，先开启 SQL Audit；

#### 1.1 设置 Session 级别变量
设置 Session 级别变量，仅当前连接生效，重新连接需要重新设置；
```sql
-- 登录业务租户
obclient -h10.x.x.x -P2883 -uroot@mytenant#obcluster -p

-- 查询变量
SHOW VARIABLES LIKE '%ob_enable_trace_log%';
SHOW VARIABLES LIKE '%ob_enable_plan_cache%';

-- 开启
SET ob_enable_trace_log = ON;
SET ob_enable_plan_cache = ON;

-- 关闭
SET ob_enable_trace_log = OFF;
SET ob_enable_plan_cache = OFF;
```


#### 1.2 设置 GLOBAL 级别变量
设置 GLOBAL 级别变量，重新连接或连接生效；
```sql
-- 登录业务租户
obclient -h10.x.x.x -P2883 -uroot@mytenant#obcluster -p

-- 开启
SET GLOBAL ob_enable_trace_log = ON;
SET GLOBAL ob_enable_plan_cache = ON;

-- 关闭
SET GLOBAL ob_enable_trace_log = OFF;
SET GLOBAL ob_enable_plan_cache = OFF;
```



### 2 查看 SQL 执行的物理执行计划
#### 2.1 查询执行计划信息
查询 sql 实际执行计划必须给定 IP、Port、`tenant_id` 和 `plan_id` 这四列的值，可以通过下面的方式获取到该信息；
##### 2.1.1 方法1：
通过 gv$sql_audit 视图查询信息；

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/05-ocean-base/gv-sql-audit/#1-1-gv-sql-audit" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 1.1 查询 gv$sql_audit 视图
###### 1.1.1 登入用户
连接 OceanBase 数据库详细情况参考：[[15_OceanBase/02_OceanBase 基本操作/连接 OceanBase 数据库\|连接 OceanBase 数据库]]，；
```sql
mysql -h 100.64.132.106 -P2883 -uroot@sys#zwy_hz_23011802 -p -A -c -Doceanbas  -- 需要输入密码，连接集群系统租户
obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p    -- 登录业务 MySql 租户
obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p   -- 通过普通用户登录 Oracle 租户
```

###### 1.1.2 MySql 租户
```sql
-- 查询某次 sql 执行对应的 
SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, type, elapsed_time, execute_time, query_sql 
FROM gv$sql_audit 
WHERE tenant_id=1001 and user_name='xxxx' and query_sql like'%'；

select * from oceanbase.gv$sql_audit where sql_id='ACC13731E9CEC5DD5BE06DE726E9DE6D' limit 5;
select /*+ QUERY_TIMEOUT(10000000) */ * from oceanbase.v$sql_audit  where SQL_ID='EEE062DD3082DFDB90B4F454F95081B8' limit 5\G  
select /*+ QUERY_TIMEOUT(10000000) */ * from oceanbase.gv$sql_audit where SQL_ID='EEE062DD3082DFDB90B4F454F95081B8' limit 5\G  
select /*+ QUERY_TIMEOUT(10000000) */ * from oceanbase.gv$sql_audit where QUERY_SQL LIKE '%tab_1%' limit 5\G  
select /*+ QUERY_TIMEOUT(10000000) */ * from oceanbase.gv$sql_audit where QUERY_SQL = "insert into tab_1(c1,c2) values(20,'20')" limit 5\G  

-- 方式1：通过 trace_id
SELECT usec_to_time(request_time) request_time_s, t.svr_ip ip, t.svr_port port, t.tenant_id, t.plan_id, *
FROM oceanbase.gv$sql_audit t
WHERE trace_id = 'YB42AC1BCD4E-0005F8AB8D83F8E5-0-0'
ORDER BY request_time DESC;

-- 方式2：通过 SQL
SELECT usec_to_time(request_time) request_time_s, t.svr_ip ip, t.svr_port port, t.tenant_id, t.plan_id, *
FROM oceanbase.gv$sql_audit t
WHERE query_sql LIKE '%INSERT INTO test%'     -- 
ORDER BY request_time DESC;
```

###### 1.1.3 Oracle 租户
```sql
-- 方式1：通过 trace_id
SELECT to_char(to_date('1970-01-01', 'yyyy-mm-dd') + (request_time / 1000000 / 86400) + to_number(substr(tz_offset(sessiontimezone), 1, 3)) / 24, 'YYYYMMDD HH24:MI:SS') request_time_s,
  t.svr_ip, t.svr_port, t.tenant_id, t.plan_id, t.*
FROM gv$sql_audit t
WHERE trace_id = 'YB42AC1BCD4E-0005F8AB8D83FD79-0-0'
ORDER BY request_time DESC;

-- 方式2：通过 SQL
SELECT to_char(to_date('1970-01-01', 'yyyy-mm-dd') + (request_time / 1000000 / 86400) + to_number(substr(tz_offset(sessiontimezone), 1, 3)) / 24, 'YYYYMMDD HH24:MI:SS') request_time_s,
  t.svr_ip, t.svr_port, t.tenant_id, t.plan_id, t.*
FROM gv$sql_audit t
WHERE query_sql LIKE '%INSERT INTO test%'
ORDER BY request_time DESC;
```




</div></div>


##### 2.1.2 方法2：
通过 gv$plan_cache_plan_stat 查询信息，2.x，3.x 版本；

```sql
-- 1. 登入租户
obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p    -- 登录业务 MySql 租户
obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p   -- 通过普通用户登录 Oracle 租户
```


```sql
-- 使用 root@sys 登录 OceanBase 数据库的 sys 租户  

-- 查看当前集群中的租户信息  
SELECT tenant_id,tenant_name FROM oceanbase.gv$tenant;  

-- 使用 root@sys 登录 OceanBase 数据库的 sys 租户  
SELECT svr_ip,svr_port, plan_Id,TYPE,d.database_name, query_sql, first_load_time, avg_exe_usec, slow_count,executions, slowest_exe_usec, sql_id  
FROM oceanbase.gv$plan_cache_plan_stat s  
JOIN oceanbase.gv$database d ON (s.tenant_id=d.tenant_id AND s.db_id=d.database_id)  
WHERE s.tenant_id=1008  AND d.database_name LIKE 'test%'  AND query_sql LIKE '%tab_1%'  
ORDER BY avg_exe_usec desc;  
```

```sql
-- 2.通过 plan_id 查询信息
SELECT * FROM oceanbase.gv$plan_cache_plan_stat WHERE plan_id = 1093;

SELECT * FROM gv$plan_cache_plan_stat WHERE plan_id = 1376;
```

```sql
-- 3. 通过 SQL 查询信息
SELECT * FROM oceanbase.gv$plan_cache_plan_stat WHERE query_sql LIKE '%INSERT INTO test%';  -- 通过 SQL
SELECT * FROM oceanbase.v$plan_cache_plan_stat  WHERE tenant_id= 1001 AND STATEMENT LIKE 'insert into tab_1%'  limit 10\G 
SELECT /*+ QUERY_TIMEOUT(10000000) */ * FROM oceanbase.gv$plan_cache_plan_stat WHERE tenant_id= 1008 and statement LIKE '%tab_1%' limit 10\G

SELECT * FROM gv$plan_cache_plan_stat WHERE query_sql LIKE '%INSERT INTO test%';
```


##### 2.1.3 在 4.x 版本中
1. [执行计划](https://www.oceanbase.com/docs/common-oceanbase-database-1000000000033850)，；


#### 2.2 获取实际执行计划

> [!NOTE] 注意：
> 如果访问 `gv$plan_cache_plan_explain`，必须给定 IP、Port、`tenant_id` 和 `plan_id` 这四列的值；
> 如果访问 `v$plan_cache_plan_explain`，必须给定 `tenant_id` 和 `plan_id` 的值，否则系统将返回空集；

```sql
-- 查询 sql 语句的实际执行计划
-- 若访问 gv$plan_cache_plan_explain，必须给定 IP、Port、tenant_id 和 plan_id 这四列的值；  
SELECT * FROM oceanbase.v$plan_cache_plan_explain  
WHERE tenant_id = 1008 AND plan_id = 12568;  
  
-- 如果访问 v$plan_cache_plan_explain，必须给定 tenant_id 和 plan_id 的值，否则系统将返回空集；  
SELECT * FROM oceanbase.gv$plan_cache_plan_explain  
WHERE IP='100.64.174.102' and Port=2882 and tenant_id = 1008 AND plan_id = 12568;  
  
SELECT * FROM oceanbase.gv$plan_cache_plan_explain  
WHERE IP = host_ip() AND PORT = rpc_port() and tenant_id = 1008 AND plan_id = 12568;
```

`注意`：gv$plan_cache_plan_explain 只支持主键查询，必须提供上述四个字段进行匹配查询，否则结果显示为空；

### 3 清理 PLAN CACHE 
#### 3.1  MySql 模式
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


#### 3.2  Oracle  模式
```sql
-- 通过普通用户登录 Oracle 租户
obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p

-- 根据 sql_id 清理 PLAN CACHE
BEGIN
  dbms_plan_cache.purge(sql_id => '5F0B1122EA84DC6CF43D6F39131D11AA',schema => 'ALVIN',global => true);
END;
```


### 4 参考文档
1. [实时执行计划展示](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357003)，；


