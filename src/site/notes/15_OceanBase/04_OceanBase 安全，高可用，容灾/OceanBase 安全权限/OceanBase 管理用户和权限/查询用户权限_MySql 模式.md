---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/查询用户权限_MySql 模式/","dgPassFrontmatter":true}
---


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
更多该视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/字典视图_MySql 租户权限相关_01#1 mysql.user\|字典视图_MySql 租户权限相关_01#1 mysql.user]]，；


#### 3 查看用户所拥有的数据库级权限
【管理员】可以通过 *mysql.db* 视图查看某个用户所拥有的数据库级权限；【普通用户】可以通过 *mysql.db* 视图查看自己所拥有的数据库级权限；

```sql
 -- 查看用户所拥有的数据库级权限
 SELECT * FROM mysql.db WHERE user='user1'\G;
```
更多该视图的字段及说明请参见：[[15_OceanBase/99_内部表介绍/字典视图_MySql 租户权限相关_01#2 mysql.db\|字典视图_MySql 租户权限相关_01#2 mysql.db]]，；





### 参考文档
1. *查看用户权限*：[v4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821413)，；
