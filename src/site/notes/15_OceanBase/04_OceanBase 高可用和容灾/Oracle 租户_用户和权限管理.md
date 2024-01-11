---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/Oracle 租户_用户和权限管理/","dgPassFrontmatter":true}
---


### Oracle 租户_用户和权限管理
#### 1 用户管理_Oracle 租户  
##### 1.1 创建，删除用户  
创建用户：需要 CREATE USER 权限才能创建用户，由于 CREATE USER 权限较大，默认仅集群管理员和租户管理员拥有此系统权限，其他用户若需要创建用户，则需要被授予 CREATE USER 权限；CREATE USER 语句的更多信息，请参见 [CREATE USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222061)，；

```sql  
-- 创建用户并指定明文密码  
CREATE USER user1 IDENTIFIED BY user1;  
CREATE USER user2 IDENTIFIED BY VALUES "user2";  
  
-- 授予用户权限  
GRANT CREATE SESSION TO user1; -- 授予用户 user1 连接数据库的权限  
GRANT CREATE TABLE TO user1;  
  
-- 查看创建的用户  
SELECT username FROM dba_users; -- 查询所有用户  
SELECT USER FROM DUAL; -- 查询当前使用的用户  
```  

删除用户：必须拥有全局的 CREATE USER 权限，才可以成功执行 DROP USER 命令，删除用户会删除该用户拥有的所有 Object，包括权限、数据库、表等，请谨慎操作，更多详细情况，[删除用户_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220865)，；更多信息请参见 [DROP USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222090)，；  

```sql  
-- 删除用户  
/*  
语法：DROP USER username [CASCADE];  
参数说明：  
username 表示待删除的用户，仅支持删除单个用户；  
CASCADE 表示在删除用户前，系统会删除该用户拥有的所有 Object，包括权限、数据库、表等；  
*/  
DROP USER user1;  
DROP USER user1 CASCADE; -- 删除用户，需谨慎操作；  
```  


##### 1.2 切换用户  
切换用户的功能是原生 Oracle 的功能，OceanBase 暂时不支持的功能；  

```sql  
-- 查看创建的用户  
SELECT username FROM dba_users; -- 查询所有用户  
SELECT USER FROM DUAL; -- 查询当前使用的用户  
  
-- 切换用户  
-- 语法：ALTER SESSION SET CURRENT_SCHEMA = account_name;  
ALTER SESSION SET CURRENT_SCHEMA = USER1;  
```  

##### 1.3 锁定和解锁用户  
若用户暂时不需要访问数据库，管理员可锁定该用户，被锁定的用户无法登录 OceanBase 数据库，管理员也可以解锁用户，重新激活被锁定的用户；  
锁定和解锁用户的操作一般由管理员完成，普通用户若需要执行锁定和解锁操作，必须具备全局 ALTER USER 权限，更多 ALTER USER 语句的信息请参见 [ALTER USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222075)，；  

```sql  
-- 查询用户锁定状态  
SELECT user_name,is_locked FROM ALL_VIRTUAL_USER_REAL_AGENT WHERE user_name='DEMO';  
-- is_locked 字段:YES，表示用户为锁定状态；NO，表示用户为解锁状态；  
  
-- 锁定或解锁用户，语法：ALTER USER user_name ACCOUNT LOCK | UNLOCK;  
ALTER USER demo ACCOUNT LOCK; -- 锁定用户  
ALTER USER demo ACCOUNT UNLOCK; -- 解锁用户  
  
-- 重新登录用户验证用户锁定状态  
obclient -udemo@demo0_111 -P2881 -h10.10.10.1 -p******  
```  


#### 2 用户权限管理  
##### 2.1 查看用户权限  
管理员可以通过 DBA_SYS_PRIVS 视图查看某个用户被授予的系统权限，详细说明请参见 [DBA_SYS_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219432)，；  
普通用户和管理员都可以通过 USER_SYS_PRIVS 视图查看自己被授予的系统权限，详细说明请参见 [USER_SYS_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219277)；  

```sql  
-- 查看某个用户被授予的系统权限  
SELECT * FROM DBA_SYS_PRIVS WHERE grantee='SECUSER';  
  
-- 查看当前用户被授予的系统权限  
SELECT * FROM USER_SYS_PRIVS;  
```  

管理员可以通过 DBA_TAB_PRIVS 视图查看某个用户被授予的对象权限，说明请参见 [DBA_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219217)，；  
普通用户和管理员都可以通过 USER_TAB_PRIVS 视图查看当前自己被授予的对象权限，说明请参见 [USER_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219409)，；  

```sql  
-- 查看某个用户被授予的对象权限  
SELECT * FROM DBA_TAB_PRIVS WHERE grantee='SECUSER';  
  
-- 查看当前用户被授予的对象权限  
SELECT * FROM USER_TAB_PRIVS;  
```  

管理员可以通过 DBA_ROLE_PRIVS 视图查看某个用户所拥有的角色，说明请参见 [DBA_ROLE_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219223)，；  
管理员和普通用户都可以通过 USER_ROLE_PRIVS 视图查看自己被授予的角色，说明请参见 [USER_ROLE_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219420)，；

```sql  
-- 查看某个用户所拥有的角色  
SELECT * FROM DBA_ROLE_PRIVS WHERE grantee='USER2';  
  
-- 查看当前用户被授予的角色  
SELECT * FROM USER_ROLE_PRIVS;  
```  

如果需要查看当前角色所拥有的对象权限或系统权限，可以参见 [查看角色](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221476)，；


##### 2.2 授予，撤销权限  
直接授予权限详情请参考：[直接授予权限_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220862)，关于 GRANT 语句的更多信息，参见 [GRANT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222051)，；  

```sql  
-- 授予系统权限  
GRANT CREATE SEQUENCE TO test; -- 将系统权限 CREATE SEQUENCE 授予用户 test  
  
-- 授予对象权限  
GRANT SELECT, UPDATE ON emp_view TO test; -- 将视图 emp_view 的 SELECT、UPDATE 权限授予用户 test  
```  
  
可以通过 REVOKE 语句将角色从用户或其他角色中撤销；撤销角色时，当前用户必须拥有被撤销的角色，并且拥有 GRANT OPTION 权限，或者拥有 GRANT ANY ROLE 权限，才能撤销成功；  
更多 REVOKE 语句的说明信息，请参见 [REVOKE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222036)，；撤销权限详情请参考：[修改用户权限_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220869)，；  

```sql  
-- 撤销系统权限  
REVOKE CREATE SEQUENCE FROM test; -- 撤销用户 test 的系统权限 CREATE SEQUENCE  
  
-- 撤销对象权限  
REVOKE ALL PRIVILEGES FROM user_name;  
-- 撤销用户 test 的对于视图 emp_view 的对象权限 SELECT 权限和 UPDATE 权限  
```  


##### 2.3 权限转授  
权限转授解决了授权者集中的问题；通过在授权时指定 with admin/grant option，可以同时授予用户将对应权限转授给其他用户的权限；  
回收对象权限时，要同时回收该用户转授给其他用户的对应权限，即 A 授予 B 权限，B 授予 C 权限，如果 A 收回 B 的权限，C 的权限也会被回收；回收系统权限时，不会级联回收转授的权限；  
权限转授详情请参考：[权限转授_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220868)，；

```sql  
-- 使用 sys 用户登录集群的 Oracle 租户  
-- 创建 A，B，C 三个用户并分别授予 CREATE SESSION 系统权限  
CREATE USER A IDENTIFIED BY ******;  
CREATE USER B IDENTIFIED BY ******;  
CREATE USER C IDENTIFIED BY ******;  
GRANT CREATE SESSION TO A;  
GRANT CREATE SESSION TO B;  
GRANT CREATE SESSION TO C;  
  
GRANT GRANT ANY PRIVILEGE TO A; -- 单独授予 A 用户 GRANT ANY PRIVILEGE 权限  
CREATE TABLE test (id number(10),name varchar2(10)); -- 在 sys 用户下创建表 test  
GRANT SELECT ON sys.test TO A; -- 授予 A 用户查询 sys 用户下表 test 的权限  
  
-- 登录 A 用户  
GRANT SELECT ON sys.test TO B with grant option; -- 登录 A 用户，授予 B 用户查询 test 表权限  
-- with grant option 可以让 B 用户在进行授权  
SELECT * FROM sys.test; -- 切换到 B 用户，发现可以查询 test 表  
  
-- 登录 B 用户  
GRANT SELECT ON sys.test TO C; -- 登录 B 用户，把查询 test 表的权限授予 C 用户  
-- 报错：ORA-01031: insufficient privileges  
SELECT * FROM sys.test; -- 切换到 C 用户，发现可以查询 test 表，权限转授成功  
```  


### 参考文档  
1. [创建用户_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220863)，[删除用户_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220865)，；


