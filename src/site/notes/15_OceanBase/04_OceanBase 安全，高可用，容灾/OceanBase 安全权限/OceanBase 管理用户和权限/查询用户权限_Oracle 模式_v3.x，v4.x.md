---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/查询用户权限_Oracle 模式_v3.x，v4.x/","dgPassFrontmatter":true}
---


### 查询用户权限_Oracle 模式
可以通过 SQL 语句查看用户权限，也可以通过 OCP 查看用户权限；

#### 1 查看用户被授予的系统权限
【管理员】可以通过 *DBA_SYS_PRIVS* 视图查看某个用户被授予的系统权限；【普通用户】和【管理员】都可以通过 *USER_SYS_PRIVS* 视图查看自己被授予的系统权限；
更多这 2 个视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/查询 Oracle 租户的用户权限#1 查看用户被授予的系统权限\|查询 Oracle 租户的用户权限#1 查看用户被授予的系统权限]]，；

```sql
-- 【租户管理员】
-- 查看某个用户被授予的系统权限
obclient> SELECT * FROM DBA_SYS_PRIVS WHERE grantee='SECUSER';
```

```sql
-- 【租户管理员】【普通用户】
-- 查看当前用户被授予的系统权限
SELECT * FROM USER_SYS_PRIVS;
```


#### 2 查看用户被授予的对象权限
【管理员】可以通过 *DBA_TAB_PRIVS* 视图查看某个用户被授予的对象权限；【普通用户】和【管理员】都可以通过 *USER_TAB_PRIVS* 视图查看当前自己被授予的对象权限；
更多这 2 个视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/查询 Oracle 租户的用户权限#2 查看用户被授予的对象权限\|查询 Oracle 租户的用户权限#2 查看用户被授予的对象权限]]，；

```sql
-- 【租户管理员】
-- 查看某个用户被授予的对象权限
obclient> SELECT * FROM DBA_TAB_PRIVS WHERE grantee='SECUSER';
```

```sql
-- 【租户管理员】【普通用户】
-- 查看当前用户被授予的对象权限
obclient> SELECT * FROM USER_TAB_PRIVS;
```


#### 3 查看某个用户所拥有的角色
【管理员】可以通过 *DBA_ROLE_PRIVS* 视图查看某个用户所拥有的角色；【管理员】和【普通用户】都可以通过 *USER_ROLE_PRIVS* 视图查看自己被授予的角色；
更多这 2 个视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/查询 Oracle 租户的用户权限#3 查看某个用户所拥有的角色\|查询 Oracle 租户的用户权限#3 查看某个用户所拥有的角色]]，；

```sql
-- 【租户管理员】
-- 查看某个用户所拥有的角色
obclient> SELECT * FROM DBA_ROLE_PRIVS WHERE grantee='USER2';
```

```sql
-- 【租户管理员】【普通用户】
-- 查看当前用户被授予的角色
obclient> SELECT * FROM USER_ROLE_PRIVS;
```


### 参考文档
1. *查看用户权限*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052869)，；
