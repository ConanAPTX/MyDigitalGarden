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
/* 字段说明：
	GRANTEE：VARCHAR2(128)，NO，接受授权的授予者名称、用户或角色；
	PRIVILEGE：VARCHAR2(40)，NO，系统权限；
	ADMIN_OPTION：VARCHAR2(3)，NO，授予管理选项；
*/
```

```sql
-- 【租户管理员】【普通用户】
-- 查看当前用户被授予的系统权限
SELECT * FROM USER_SYS_PRIVS;
/* 字段说明:
	GRANTEE：VARCHAR2(128)，NO，接受授权的受让人名称、用户或角色；
	PRIVILEGE：VARCHAR2(40)，NO，系统权限；
	ADMIN_OPTION：VARCHAR2(3)，NO，授予管理选项；
*/
```


#### 2 查看用户被授予的对象权限
【管理员】可以通过 *DBA_TAB_PRIVS* 视图查看某个用户被授予的对象权限；【普通用户】和【管理员】都可以通过 *USER_TAB_PRIVS* 视图查看当前自己被授予的对象权限；
更多这 2 个视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/查询 Oracle 租户的用户权限#2 查看用户被授予的对象权限\|查询 Oracle 租户的用户权限#2 查看用户被授予的对象权限]]，；

```sql
-- 【租户管理员】
-- 查看某个用户被授予的对象权限
obclient> SELECT * FROM DBA_TAB_PRIVS WHERE grantee='SECUSER';
/* 字段说明：
	GRANTEE：VARCHAR2(128)，NO，被授予访问权限的用户名；
	OWNER：VARCHAR2(128)，NO，对象的所有者；
	TABLE_NAME：VARCHAR2(128)，NO，对象名称；
	GRANTOR：VARCHAR2(128)，NO，执行授权的用户名；
	PRIVILEGE：VARCHAR2(40)，NO，对象的权限；
	GRANTABLE：VARCHAR2(3)，NO，指示是否能通过 GRANT OPTION 授予权限；
	HIERARCHY：VARCHAR2(10)，NO，指示是否能通过 HIERARCHY OPTION 授予权限；
*/
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
/* 字段说明：
	GRANTEE：VARCHAR2(30)，NO，接收授予的用户或角色的名称；
	GRANTED_ROLE：VARCHAR2(30)，NO。授予角色名；
	ADMIN_OPTION：VARCHAR2(3)，NO，指示授权是否带有 ADMIN 选项；
	DEFAULT_ROLE：VARCHAR2(3)，NO，指示角色是否被指定为用户的默认角色；
*/
```

```sql
-- 【租户管理员】【普通用户】
-- 查看当前用户被授予的角色
obclient> SELECT * FROM USER_ROLE_PRIVS;
```


### 参考文档
1. *查看用户权限*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052869)，；

