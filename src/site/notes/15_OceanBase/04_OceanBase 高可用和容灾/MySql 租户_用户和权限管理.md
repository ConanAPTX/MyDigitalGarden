---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/MySql 租户_用户和权限管理/","dgPassFrontmatter":true}
---


### MySql 租户_用户和权限管理  
#### 1 用户管理  
##### 1.1 创建，删除，用户  
*创建用户*：需要 CREATE USER 权限才能创建用户，由于 CREATE USER 权限较大，默认仅集群管理员和租户管理员拥有此系统权限，其他用户若需要创建用户，则需要被授予 CREATE USER 权限；  
*删除用户*：必须拥有全局的 CREATE USER 权限，才可以成功执行 DROP USER命令；  


> [!NOTE] 用户名称的规则：
> 1. 用户名的唯一性：每个用户名需要保证在租户内唯一；
> 	1. 用户名称在租户内是唯一的，不同租户下的用户可以同名，故通过 `用户名@租户名` 的形式可以在系统全局唯一定位一个租户用户；
> 	2. 由于系统租户与 MySQL 租户属于同一兼容模式，为区别系统租户和普通租户下的用户，建议对系统租户下的用户名称使用特定前缀；
> 2. 用户名的命名约定：
> 	1. 使用 OBClient 、ODC 等客户端创建用户时，要求用户名长度不超过 64 个字节；
> 	2. 使用 OCP 创建用户时，要求用户名以英文字母开头，可包含大写字母、小写字母、数字和下划线，且长度为 2~64 个字符；

```sql  
-- 1.创建用户(使用 root 用户登录集群的 MySQL 租户)  
CREATE USER IF NOT EXISTS 'user1' IDENTIFIED BY '123456'; -- 创建用户 test2  
	-- IF NOT EXISTS：如果待创建的用户名已存在并且未指定 IF NOT EXISTS，则系统会报错；
	-- IDENTIFIED BY：使用可选的 IDENTIFIED BY 子句，可以为账户指定一个密码；

-- 2. 授予用户权限
GRANT SELECT ON db1.* TO test2;     -- 授予用户 test2 访问数据库 db1 中所有表的权限  
  
-- 3.删除用户(用户登录集群的 MySQL 租户)  
DROP USER user1;        -- 删除用户 user1  
DROP USER user1, user2; -- 删除用户 user1，user2  
	-- 删除用户后，该用户所创建的数据库对象不会被删除，但该用户的权限会一同被删除；  
```  
创建，删除用户更多详情：[创建用户_MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220873)，[删除用户_MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220877)，；  

##### 1.2 锁定，解锁用户  
若用户暂时不需要访问数据库，管理员可锁定该用户，被锁定的用户无法登录 OceanBase 数据库，管理员也可以解锁用户，重新激活被锁定的用户；  
锁定和解锁用户的操作一般由管理员完成，普通用户若需要执行锁定和解锁操作，必须具备全局 ALTER USER 权限，更多 ALTER USER 语句的信息请参见 [ALTER USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221379)，；  

```sql  
-- 查询用户锁定状态  
SELECT user_name,is_locked FROM oceanbase.DBA_OB_USERS WHERE user_name='demo';  
-- is_locked 字段:YES，表示用户为锁定状态；NO，表示用户为解锁状态；  
  
-- 锁定或解锁用户，语法：ALTER USER user_name ACCOUNT LOCK | UNLOCK;  
ALTER USER demo ACCOUNT LOCK; -- 锁定用户  
ALTER USER demo ACCOUNT UNLOCK; -- 解锁用户  
  
-- 重新登录用户验证用户锁定状态  
obclient -udemo@demo0_111 -P2881 -h10.10.10.1 -p******  
```  

#### 2 用户权限管理  
##### 2.1 查看用户权限  
管理员可以通过 mysql.user 视图查看某个用户所拥有的用户级权限。普通用户可以通过 mysql.user 视图查看自己所拥有的用户级权限；该视图的字段及说明请参见 [mysql.user](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220082)，；  
管理员可以通过 mysql.db 视图查看某个用户所拥有的数据库级权限。普通用户可以通过 mysql.db 视图查看自己所拥有的数据库级权限，该视图的字段及说明请参见 [mysql.db](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219934)，；  

```sql  
-- 管理员登录数据库的 MySQL 租户  
SHOW GRANTS FOR user1; -- 查看 user1 用户被授予的权限  
  
-- 查看用户所拥有的用户级权限  
SELECT * FROM mysql.user WHERE user='user1'\G;  
  
-- 查看用户所拥有的数据库级权限  
SELECT * FROM mysql.db WHERE user='user1'\G;  
```  
查看用户权限详细情况：[查看用户权限_MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220878)，；  


##### 2.2 授予，撤回用户权限  
可以通过 GRANT 语句授予用户用户级权限、数据库级权限或对象权限， 该命令的信息请参见 [GRANT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221383)；授予用户权限详细情况：[授予权限_MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220874)，；  

```sql  
-- 授予用户级权限  
GRANT ALL ON *.* TO user1; -- 将所有数据库的所有对象的权限授予用户 user1  
GRANT SELECT, INSERT ON *.* TO user1;  
  
-- 授予数据库级权限  
GRANT ALL ON db1.* TO user1;  
GRANT SELECT, INSERT ON db1.* TO user1;  
  
-- 授予表级权限  
GRANT ALL ON db1.tb1_name TO user1;  
GRANT SELECT, INSERT ON db1.tb1_name TO user1;  
```  

可以通过 REVOKE 语句回收权限，详细情况：[修改用户权限_MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220872)，；更多 REVOKE 语句的信息请参见 [REVOKE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221382)，；  
当前用户必须拥有被撤销的权限，并且拥有 GRANT OPTION 权限；  
撤销 ALL PRIVILEGES 和 GRANT OPTION 权限时，当前用户必须拥有全局 GRANT OPTION 权限，或者对权限表的 UPDATE 及 DELETE 权限；  

```sql  
-- 撤销用户级权限  
REVOKE SELECT, INSERT ON *.* FROM 'test';  
  
-- 撤销数据库级权限  
REVOKE SELECT, INSERT ON db1 FROM 'test';  
  
-- 撤销表级权限  
REVOKE SELECT, INSERT ON db1.tb1_name FROM 'test';  
```  


##### 2.3 权限转授  
权限转授解决了授权者集中的问题。通过在授权时指定 with admin/grant option，可以同时授予用户将对应权限转授给其他用户的权限；  
回收对象权限时，要同时回收该用户转授给其他用户的对应权限，即 A 授予 B 权限，B 授予 C 权限，如果 A 收回 B 的权限，C 的权限也会被回收；回收系统权限时，不会级联回收转授的权限；  
```sql  
-- 授予 A 权限，A 授予 B 权限  
-- 授予 A 用户权限(使用 root 用户登录集群的 MySQL 租)  
GRANT ALL PRIVILEGES ON *.* TO A with grant option;  
-- with grant option：允许授予的权限再次授予给其他用户；  
  
-- A 用户登录：授予用户 B 权限  
GRANT ALL PRIVILEGES ON *.* TO 'B' IDENTIFIED BY '******';  
```  
权限转授详细情况：[权限转授_MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220875)，；  


### 参考文档





