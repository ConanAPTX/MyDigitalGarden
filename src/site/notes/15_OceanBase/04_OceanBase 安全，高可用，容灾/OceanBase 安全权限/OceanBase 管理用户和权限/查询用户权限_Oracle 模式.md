---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/查询用户权限_Oracle 模式/","dgPassFrontmatter":true}
---


### 查询用户权限_Oracle 模式

#### 1 通过 SQL 语句查看用户权限
##### 1.1 登录数据库的 Oracle 租户
```sql
-- 登录数据库的 Oracle 租户

```
##### 1.2 通过 DBA_SYS_PRIVS 视图
管理员可以通过 DBA_SYS_PRIVS 视图查看某个用户被授予的系统权限；
```sql
-- 查看某个用户被授予的系统权限
obclient> SELECT * FROM DBA_SYS_PRIVS WHERE grantee='SECUSER';
```
更多 DBA_SYS_PRIVS 视图的字段及说明请参见 [DBA_SYS_PRIVS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945052)；

##### 1.3 通过 USER_SYS_PRIVS 视图
普通用户和管理员都可以通过 USER_SYS_PRIVS 视图查看自己被授予的系统权限；
```sql
-- 查看当前用户被授予的系统权限
SELECT * FROM USER_SYS_PRIVS;
```
更多 USER_SYS_PRIVS 视图的字段及说明请参见 [USER_SYS_PRIVS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945125)；

##### 1.4 通过 DBA_TAB_PRIVS 视图
管理员可以通过 DBA_TAB_PRIVS 视图查看某个用户被授予的对象权限；
```sql
-- 查看某个用户被授予的对象权限
obclient> SELECT * FROM DBA_TAB_PRIVS WHERE grantee='SECUSER';
```
更多 DBA_TAB_PRIVS 视图的字段及说明请参见 [DBA_TAB_PRIVS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945059)；

##### 1.5 通过 USER_TAB_PRIVS 视图
普通用户和管理员都可以通过 USER_TAB_PRIVS 视图查看当前自己被授予的对象权限。
```sql
-- 查看当前用户被授予的对象权限
obclient> SELECT * FROM USER_TAB_PRIVS;
```
更多 USER_TAB_PRIVS 视图的字段及说明请参见 [USER_TAB_PRIVS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944435)；

##### 1.6 通过 DBA_ROLE_PRIVS 视图
管理员可以通过 DBA_ROLE_PRIVS 视图查看某个用户所拥有的角色；
```sql
-- 查看某个用户所拥有的角色
obclient> SELECT * FROM DBA_ROLE_PRIVS WHERE grantee='USER2';
```
更多 DBA_ROLE_PRIVS 视图的字段及说明请参见 [DBA_ROLE_PRIVS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945040)；

##### 1.7 通过 USER_ROLE_PRIVS 视图
管理员和普通用户都可以通过 USER_ROLE_PRIVS 视图查看自己被授予的角色；
```sql
-- 查看当前用户被授予的角色
obclient> SELECT * FROM USER_ROLE_PRIVS;
```
更多 USER_ROLE_PRIVS 视图的字段及说明请参见 [USER_ROLE_PRIVS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945118)；
#### 2 通过 OCP 查看用户权限

1. 登录 OCP。
2. 在左导航栏上单击 **租户** ，进入 **租户** 页面。
3. 在租户列表中，选择 **租户模式** 为 **Oracle** 的租户，进入 **总览** 页面。
4. 在左侧导航栏上，单击 **用户管理** 。
5. 在用户列表中，找到待查看的用户，将鼠标悬停在 **拥有系统权限** 、 **拥有角色** 对应的列上，查看该用户所拥有的权限及角色；


### 参考文档

