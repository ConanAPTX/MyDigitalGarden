---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表组_MySql 租户/","dgPassFrontmatter":true}
---


### 管理表组_MySql 租户
#### 1 创建表组
```sql
-- 查询表组
SHOW TABLEGROUPS;  -- 查询表组
```


#### 2 创建，删除表组  
```sql
-- 创建表组  
CREATE TABLEGROUP grp1;  
CREATE TABLEGROUP IF NOT EXISTS grp2;  
  
-- 删除表组，若表组中有表，则该表组不允许被删除；  
DROP TABLEGROUP grp1;  
DROP TABLEGROUP IF EXISTS grp2;  
  
-- 将表加入表组  
ALTER TABLE t1 SET TABLEGROUP grp1; -- 将表加入表组  
  
-- 将表移除表组  
ALTER TABLE t1 SET TABLEGROUP ''; -- 将表移出表组  
ALTER TABLE t1 SET TABLEGROUP grp2; -- 将表迁移到其他表组  
```


#### 3 修改表组的 Locality 和 Primary Zone  
```sql
SQL 语法如下：  
ALTER TABLEGROUP tablegroupname alter_tablegroup_actions;  
  
alter_tablegroup_actions:  
alter_tablegroup_action  
| alter_tablegroup_action, alter_tablegroup_action  
  
alter_tablegroup_action:  
SET LOCALITY [=] locality_name  
|SET PRIMARY_ZONE [=] primary_zone_name;  
  
其中：  
modify_tg_partition_info 用于修改表组的分区规则；  
LOCALITY locality_name 用于指定表组的 locality；  
PRIMARY_ZONE primary_zone_name 用于指定表组的主 Zone；  
-- 将表组 grp1 中所有表的 Locality 都修改为 F@z1,F@z3 的示例如下：  
ALTER TABLEGROUP grp1 SET locality = 'F@z1,F@z3'; -- 修改 Locality  
  
-- 将表组 grp1 中所有表的 Primary Zone 都修改为 z1,z2 的示例如下：  
ALTER TABLEGROUP grp1 SET primary_zone = 'z1,z2'; -- 修改 Primary Zone
```


### 参考文档

