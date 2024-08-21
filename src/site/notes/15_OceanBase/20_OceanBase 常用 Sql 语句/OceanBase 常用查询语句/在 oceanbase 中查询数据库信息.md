---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/在 oceanbase 中查询数据库信息/","dgPassFrontmatter":true}
---



### 文档名称

#### 1 查询当前数据库或用户
```sql
-- 1.oracle 模式
SELECT USER FROM DUAL;  -- 查询当前登录的用户

-- 2.MySQL 模式
SELECT database();      -- 查看当前数据库
SELECT user();          -- 查询当前登录的用户
```

#### 2 查询租户中的数据库信息
*gv$database* 视图从 V4.0.0 版本开始废弃；
```sql
-- 查询数据库信息
-- 1.使用 root 用户登录集群的 sys 租户；【所有租户的所有数据库】
select * from oceanbase.gv$database where tenant_id=1002 limit 10;  
select tenant_id, tenant_name,database_id,database_name,zone_list,primary_zone,collation_type,comment,read_only  
from oceanbase.gv$database  
where tenant_id=1002  
limit 10;  
```


从 V4.0.0 版本开始引入 _oceanbase.CDB_OB_DATABASES_ 视图：系统租户下展示所有租户的数据库元信息；【该视图仅存在 sys 租户】
```sql
select * from oceanbase.CDB_OB_DATABASES where tenant_id=1002 limit 10;  
```
关于该视图更多信息：[[15_OceanBase/99_内部表介绍/数据库基本信息_01#3 CDB_OB_DATABASES\|数据库基本信息_01#3 CDB_OB_DATABASES]]，；

从 V4.0.0 版本开始引入 _oceanbase.DBA_OB_DATABASES_ 视图：展示租户的数据库元信息；
```sql
-- sys 租户及 MySql 租户
select * from oceanbase.CDB_OB_DATABASES where tenant_id=1002 limit 10;  

-- Oracle 租户
select * from sys.CDB_OB_DATABASES
```
关于该视图更多信息：[[15_OceanBase/99_内部表介绍/数据库基本信息_01#4 DBA_OB_DATABASES\|数据库基本信息_01#4 DBA_OB_DATABASES]]，；



### 参考文档



