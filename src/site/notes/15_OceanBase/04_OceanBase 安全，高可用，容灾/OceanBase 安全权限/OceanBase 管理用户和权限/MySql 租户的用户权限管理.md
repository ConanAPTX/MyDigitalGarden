---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/MySql 租户的用户权限管理/","dgPassFrontmatter":true}
---



### MySql 租户的用户权限管理

#### 1 查看用户权限  

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base/ocean-base/ocean-base/my-sql/#my-sql" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 查询用户权限_MySql 模式
可以通过 SQL 语句查看用户权限，也可以通过 OCP 查看用户权限；

#### 1 查看用户，角色被授予的权限
通过 `SHOW GRANTS` 语句查看【用户或角色】被授予的【角色或权限】；

```sql
-- 1.查看用户被授予的角色和权限
	-- 1.管理员登录数据库的 MySQL 租户
	
	-- 2.查看当前用户被授予的权限【3 个命令效果一致】
		SHOW GRANTS;
		SHOW GRANTS FOR CURRENT_USER;
		SHOW GRANTS FOR CURRENT_USER();
	
	-- 3.查看某个用户被授予的权限
		SHOW GRANTS FOR user1;
		SHOW GRANTS FOR test1 USING role1;    -- 添加一个 USING 子句，指定展示该角色所包含的权限；
		+-------------------------------------------------+
		| Grants for test1@%                              |
		+-------------------------------------------------+
		| GRANT ALTER SYSTEM ON *.* TO 'test1'            |
		| GRANT UPDATE, SELECT ON `dbtest`.* TO 'test1'   |
		| GRANT `employee`@`%`,`role1`@`%` TO `test1`@`%` |
		+-------------------------------------------------+

-- 2.查看角色被授予的角色或权限
	SHOW GRANTS FOR role1;    -- 查看角色被授予的角色或权限；
	+-------------------------------------------------------+
	| Grants for role1@%                                    |
	+-------------------------------------------------------+
	| GRANT ALTER SYSTEM ON *.* TO 'role1'                  |
	| GRANT SELECT ON `dbtest`.* TO 'role1'                 |
	| GRANT SELECT ON `test1`.`tbl1` TO 'role1'             |
	| GRANT `employee`@`%` TO `role1`@`%` WITH ADMIN OPTION |
	+-------------------------------------------------------+
```


#### 2 查看用户所拥有的用户级权限
【管理员】可以通过 *mysql.user* 视图查看某个用户所拥有的用户级权限；【普通用户】可以通过 *mysql.user* 视图查看自己所拥有的用户级权限；

```sql
-- 查看用户所拥有的用户级权限
SELECT * FROM mysql.user WHERE user='user1'\G;
```
更多该视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/MySql 租户权限相关_01#1 mysql.user\|MySql 租户权限相关_01#1 mysql.user]]，；


#### 3 查看用户所拥有的数据库级权限
【管理员】可以通过 *mysql.db* 视图查看某个用户所拥有的数据库级权限；【普通用户】可以通过 *mysql.db* 视图查看自己所拥有的数据库级权限；

```sql
 -- 查看用户所拥有的数据库级权限
 SELECT * FROM mysql.db WHERE user='user1'\G;
```
更多该视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/MySql 租户权限相关_01#2 mysql.db\|MySql 租户权限相关_01#2 mysql.db]]，；






</div></div>



#### 2 授予，撤回用户权限  
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


#### 3 权限转授  
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


### 2 参考文档



