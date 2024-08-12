---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 gv$sql_audit 视图_2.x，3.x/","dgPassFrontmatter":true}
---



### 查询 gv$sql_audit 视图

该视图详细介绍：[[15_OceanBase/99_内部表介绍/gv$sql_audit，gv$ob_sql_audit\|gv$sql_audit，gv$ob_sql_audit]]，；

#### 1 登入用户
[[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库\|通过黑屏客户端连接 OB 数据库]]，；
```sql
mysql -h 100.64.132.106 -P2883 -uroot@sys#zwy_hz_23011802 -p -A -c -Doceanbas  -- 需要输入密码，连接集群系统租户
obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p    -- 登录业务 MySql 租户
obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p   -- 通过普通用户登录 Oracle 租户
```

#### 2 查询执行过 SQL 语句
##### 2.1 MySql 租户
```sql
-- 查询某次 sql 执行对应的 
SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, elapsed_time, execute_time, query_sql 
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

/*
RETRY_CNT：retry 次数是否很多, 如果次数很多，则可能有锁冲突或切主等情况；
QUEUE_TIME：queue time 的值是否过大，很高表明CPU资源不够用；
GET_PLAN_TIME：获取执行计划时间, 如果时间很长，一般会伴随 IS_HIT_PLAN ＝ 0, 表示没有命中 plan cache；
EXECUTE_TIME：查看 EXECUTE_TIME 值，如果值过大，则：
	l.查看是否有很长等待事件耗时；
	2.分析逻辑读次数是否异常多(突然有大账户时可能会出现）；

SQL audit 记录的等待事件如下相关信息：
	记录了 4 大类等待事件分别的耗时（APPLICATION_WAIT_TIME, CONCURRENCY_WAIT_TIME, USER_IO_WAIT_TIME, SCHEDULE_TIME), 每类等待事件都涉及很多具体的等待事件；
	记录了耗时最多的等待事件名称（EVENT）及该等待事件耗时（WAIT_TIME_MICRO）；
	记录了所有等待事件发生的次数（TOTAL_WAITS）及所有等待事件总耗时（TOTAL_WAIT_TIME_MICRO）；
*/
```

##### 2.2 Oracle 租户
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
WHERE tenant_id=1005 and query_sql LIKE '%INSERT INTO test%'
	and rownum <= 10
ORDER BY request_time DESC;

SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, type, elapsed_time, execute_time, query_sql  
FROM gv$sql_audit  
WHERE tenant_id=1005 and query_sql LIKE '%INSERT INTO test%'
	and rownum <= 10
ORDER BY request_time DESC;
```
*OceanBase V3.1.2 之前版本使用模糊查询 LIKE*：[[15_OceanBase/80_OB 相关知识库/OMS 错误码#1.1 CONNECTOR-SOOO0400601\|OMS 错误码#1.1 CONNECTOR-SOOO0400601]]，；


#### 3 查询 top SQL
[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 TOP SQL\|查询 TOP SQL]]，；


### 参考文档



