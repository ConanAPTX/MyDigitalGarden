---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/Oceanase 查询数据库对象情况/","dgPassFrontmatter":true}
---



### 文档名称
#### 1 

1. 查询数据库对象：
	1. *该视图从 V2.2.77 版本开始引入*；*该视图仅适用于 OceanBase 数据库 Oracle 模式*；
		1. `USER_OBJECTS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750437)，展示用户拥有的所有数据库对象，视图与 ALL_OBJECTS 一致，但没有 OWNER 列，因为 OWNER 就是自己本身；
		2. `ALL_OBJECTS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750231)，用于展示用户可访问的所有数据库对象；
		3. `DBA_OBJECTS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750204)，用于查看数据库所有数据库对象；
	2. `oceanbase.DBA_OBJECTS`：[SYS 租户_V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750547)，[MySQL 租户_V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000751140)，用于展示数据库所有数据库对象，视图定义与视图 `ALL_OBJECTS` 一致；
		1. *该视图从 V4.0.0 版本开始引入*； 

```sql
-- Oracle 租户
-- 查询数据库对象

obclient [SYS]> SELECT * FROM SYS.USER_OBJECTS WHERE ROWNUM <= 2;

-- 用于查看数据库所有数据库对象(该视图仅适用于 OceanBase 数据库 Oracle 模式)
obclient [SYS]> SELECT * FROM SYS.DBA_OBJECTS WHERE ROWNUM <= 2;
```


```sql
-- SYS 租户 或 MySql 租户
-- 查询数据库对象
SELECT * FROM oceanbase.DBA_OBJECTS limit 2;
```



### 参考文档



