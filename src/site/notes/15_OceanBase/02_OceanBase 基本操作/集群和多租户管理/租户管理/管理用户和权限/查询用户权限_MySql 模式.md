---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/管理用户和权限/查询用户权限_MySql 模式/","dgPassFrontmatter":true}
---


### 查询用户权限_MySql 模式
#### 1 通过 SQL 语句查看用户权限
##### 1.1 通过 SHOW GRANTS 语句
```sql
-- 1.管理员登录数据库的 MySQL 租户

-- 2.查看某个用户被授予的权限
SHOW GRANTS FOR user1;
```

##### 1.2 通过 mysql.user 视图查询权限
管理员可以通过 mysql.user 视图查看某个用户所拥有的用户级权限；
普通用户可以通过 mysql.user 视图查看自己所拥有的用户级权限；
```sql
-- 查看用户所拥有的用户级权限
SELECT * FROM mysql.user WHERE user='user1'\G;
```
更多 mysql.user 视图的字段及说明请参见 mysql.user； 

##### 1.3 通过 mysql.db 视图
管理员可以通过 mysql.db 视图查看某个用户所拥有的数据库级权限；
普通用户可以通过 mysql.db 视图查看自己所拥有的数据库级权限；
```sql
 -- 查看用户所拥有的数据库级权限
 SELECT * FROM mysql.db WHERE user='user1'\G;
```

#### 2 通过 OCP 查看用户权限

1. 登录 OCP。
2. 在左导航栏上单击 **租户** ，进入 **租户** 页面。
3. 在租户列表中，选择 **租户模式** 为 **Oracle** 的租户，进入 **总览** 页面。
4. 在左侧导航栏上，单击 **用户管理** 。
5. 在用户列表中，找到待查看的用户，将鼠标悬停在 **拥有系统权限** 、 **拥有角色** 对应的列上，查看该用户所拥有的权限及角色；


### 参考文档

