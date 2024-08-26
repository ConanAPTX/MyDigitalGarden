---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase SQL 优化常用语句_v4.x 版本/","dgPassFrontmatter":true}
---


### OceanBase SQL 优化常用语句


#### 1 查询 gv$sql_audit 捞 sql 语句
```sql
-- MySql 模式
SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, elapsed_time, execute_time, query_sql 
FROM oceanbase.gv$ob_sql_audit
WHERE tenant_id=1001 and user_name='xxxx' and query_sql like'%aa%'
limit 100;

-- Oracle 模式
SELECT svr_ip,svr_port,tenant_id,user_name, db_name, sql_id, plan_id, elapsed_time, execute_time, query_sql 
FROM sys.gv$ob_sql_audit
WHERE tenant_id=1005 and query_sql LIKE '%INSERT INTO test%' 
	and rownum <= 10 
ORDER BY request_time DESC;
```
更多详细：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 gv, ob_sql_audit 视图_4.x\|查询 gv, ob_sql_audit 视图_4.x]]，；


#### 2 查看执行计划
```sql
-- 查看执行计划
EXPLAIN SELECT * FROM t1,t2 WHERE t1.a = t2.a\G; 
EXPLAIN EXTENDED SELECT * FROM t1,t2 WHERE t1.a = t2.a\G;
```
更多详细：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/EXPLAIN，查看执行计划\|EXPLAIN，查看执行计划]]，；


#### 3 查询 sql 语句的实际执行计划
```sql
-- 2.1 V2.x，V3.x 版本
-- 若访问 gv$plan_cache_plan_explain，必须给定 IP、Port、tenant_id 和 plan_id 这四列的值，否则系统将返回空集； 
SELECT * FROM oceanbase.gv$ob_plan_cache_plan_explain WHERE IP='100.64.174.102' and Port=2882 and tenant_id = 1008 AND plan_id = 12568; 
SELECT * FROM oceanbase.gv$ob_plan_cache_plan_explain WHERE IP=host_ip() AND PORT = rpc_port() and tenant_id = 1008 AND plan_id = 12568;
```
更多详细：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查看 SQL 执行的物理执行计划_4.x 版本\|查看 SQL 执行的物理执行计划_4.x 版本]]，；


#### 4 查询表创建语句
```sql
-- 查询表创建语句  
show create table zxnew.ajjb;  
show create table zd.zd_ay;  
```
更多详细：[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表_MySql 模式\|管理表_MySql 模式]]，[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理表_Oracle 租户\|管理表_Oracle 租户]]，；

#### 5 查询表索引信息
MySql 模式：
```sql
-- 查看表的索引 
SHOW INDEX FROM test;
```
更多详细：[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 查看索引_MySql 模式\|OceanBase 查看索引_MySql 模式]]，；

Oracle 模式：
```sql
-- DBA_INDEXES：用于查看数据库所有表的索引信息；  
SELECT OWNER,INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,TABLE_TYPE,UNIQUENESS,COMPRESSION,STATUS,PARTITIONED,VISIBILITY  
FROM DBA_INDEXES  
WHERE TABLE_OWNER = UPPER('zxnew') AND TABLE_NAME IN (UPPER('ajjb'), UPPER('zd_ay'))
ORDER BY TABLE_OWNER,TABLE_NAME,INDEX_NAME; 
  
-- DBA_IND_COLUMNS：查看数据库所有表的索引的索引列信息；  
SELECT * FROM DBA_IND_COLUMNS 
WHERE TABLE_OWNER = UPPER('ZXNEW') AND TABLE_NAME IN (UPPER('AJJB'),UPPER('ZD_AY')) 
ORDER BY TABLE_OWNER,TABLE_NAME,INDEX_NAME, COLUMN_POSITION;  
```
更多详细：[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/查看索引_Oracle 租户\|查看索引_Oracle 租户]]，；

#### 6 查询表数据量
```sql

```
更多详细：[[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查询磁盘使用情况_V4.x\|查询磁盘使用情况_V4.x]]，；

#### 7 hint 强制指定索引
更多详细：[[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/与访问路径相关的 Hint\|与访问路径相关的 Hint]]，与访问路径相关的 Hint 包括 `INDEX Hint` 和 `FULL Hint`；

### 参考文档



