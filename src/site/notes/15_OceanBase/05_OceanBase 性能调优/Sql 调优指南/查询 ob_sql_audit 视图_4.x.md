---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 ob_sql_audit 视图_4.x/","dgPassFrontmatter":true}
---



### 查询 gv$sql_audit 视图_4.x
#### 1 查询 gv$sql_audit 视图_4.x
从 V4.0.0 版本开始引入 `v$OB_SQL_AUDIT` 视图，该视图用于展示当前 OBServer 节点的每一次 SQL 请求的来源、执行状态等统计信息；

该视图详细介绍：[[15_OceanBase/99_内部表介绍/gv$sql_audit，gv$ob_sql_audit\|gv$sql_audit，gv$ob_sql_audit]]，；


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/05-ocean-base/sql/gv-sql-audit-2-x-3-x/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 登入用户
[[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库\|通过黑屏客户端连接 OB 数据库]]，；
```sql
mysql -h 100.64.132.106 -P2883 -uroot@sys#zwy_hz_23011802 -p -A -c -Doceanbas  -- 需要输入密码，连接集群系统租户
obclient -h10.x.x.x -P2883 -uroot@obmysql#obcluster -p    -- 登录业务 MySql 租户
obclient -h10.x.x.x -P2883 -uroot@oboracle#obcluster -p   -- 通过普通用户登录 Oracle 租户
```


</div></div>


```sql
/* 开启全链路追踪 Session 级别 Trace，记录当前 Session 所有 SQL 的相关耗时等信息，采样频率为 50%。*/
CALL DBMS_MONITOR.OB_SESSION_TRACE_ENABLE(null,1,0.5,'ALL');
Query OK, 0 rows affected

 SELECT request_id,usec_to_time(request_time),ELAPSED_TIME,QUEUE_TIME,EXECUTE_TIME,FLT_TRACE_ID,QUERY_SQL 
 FROM v$OB_SQL_AUDIT 
 where ELAPSED_TIME > 100000 limit 10 ;
+------------+----------------------------+--------------+------------+--------------+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| request_id | usec_to_time(request_time) | ELAPSED_TIME | QUEUE_TIME | EXECUTE_TIME | FLT_TRACE_ID                         | QUERY_SQL                                                                                                                                                          |
+------------+----------------------------+--------------+------------+--------------+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|    5950244 | 2023-09-07 16:20:47.465958 |       127623 |         26 |       127206 | 000604c0-8981-9184-518a-e234439d873c | CREATE TABLE tbl2(c1 INT PRIMARY KEY,c2 INT)                                                                                                                       |
|    5951861 | 2023-09-07 16:21:07.887121 |       333776 |         38 |       310298 |                                      | ALTER TABLE tbl2 ADD CONSTRAINT fk1 FOREIGN KEY (c2) REFERENCES tbl3(c1) ON UPDATE SET NULL                                                                        |
|    5953177 | 2023-09-07 16:21:28.215377 |       174416 |         24 |       174186 | 000604c0-8bef-5afb-f9d3-2ee0dfab4c8f | SELECT request_id,usec_to_time(request_time),ELAPSED_TIME,QUEUE_TIME,EXECUTE_TIME,FLT_TRACE_ID,QUERY_SQL FROM v$OB_SQL_AUDIT where ELAPSED_TIME > 100000           |
|    5954522 | 2023-09-07 16:21:48.317360 |       128803 |         27 |       128542 | 000604c0-8d22-1659-7b0c-a0ac0645894d | SELECT request_id,usec_to_time(request_time),ELAPSED_TIME,QUEUE_TIME,EXECUTE_TIME,FLT_TRACE_ID,QUERY_SQL FROM v$OB_SQL_AUDIT where ELAPSED_TIME > 100000  limit 10 |
+------------+----------------------------+--------------+------------+--------------+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------+
4 rows in set
```

#### 2 


### 参考文档



