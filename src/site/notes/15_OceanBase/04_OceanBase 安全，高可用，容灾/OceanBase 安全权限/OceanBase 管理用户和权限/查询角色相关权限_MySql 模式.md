---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/查询角色相关权限_MySql 模式/","dgPassFrontmatter":true}
---



### 查询角色相关权限_MySql 模式
#### 1 查看用户，角色被授予的权限

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base/ocean-base/ocean-base/my-sql/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



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



</div></div>



#### 2 查询角色相关权限
##### 2.1 查看当前会话中被激活的角色

```sql
-- 查看当前用户在当前会话中被激活的角色；
obclient [oceanbase]> SELECT current_role();
```
如果查询结果为 `NONE`，则表示当前用户没有处于激活状态的角色，只有处于激活状态的角色的权限才能被用户使用；


##### 2.2 查看用户登录时默认激活的角色
通过 Global 变量 `activate_all_roles_on_login` 启用了登录时自动激活用户被授予的所有角色，或者通过 `SET DEFAULT ROLE` 语句或 `ALTER USER` 语句设置了登录时默认激活的角色后，可以查询 `mysql.default_roles` 视图，确认用户登录默认激活的角色是否设置正确；

```sql
CREATE ROLE IF NOT EXISTS role1;                   -- 新建角色 `role1`
GRANT ALTER SYSTEM ON *.* TO role1;                -- 将 `ALTER SYSTEM` 权限授予角色 `role1`
CREATE USER 'user001'@'%' IDENTIFIED BY '123456';  -- 新建用户 `user001`
GRANT role001 TO user001 WITH ADMIN OPTION;        -- 将角色 `role1` 授予新创建的用户 `user001`
SET DEFAULT ROLE ALL TO user001;                   -- 指定用户 `user001` 在登录时默认激活用户被授予的所有角色

obclient [oceanbase]> SELECT * FROM mysql.default_roles;
+------+-------+-------------------+-------------------+
| HOST | USER  | DEFAULT_ROLE_HOST | DEFAULT_ROLE_USER |
+------+-------+-------------------+-------------------+
| %    | test1 | %                 | role1             |
| %    | test1 | %                 | employee          |
+------+-------+-------------------+-------------------+
2 rows in set
/*
字段说明：
	HOST：varchar(255)，NO，账户 Host 名字；
	USER：varchar(128)，NO，账户名；
	DEFAULT_ROLE_HOST：varchar(255)，NO，默认启用的 Role 的 Host 名字；
	DEFAULT_ROLE_USER：varchar(128)，NO，默认启用的 Role 的账户名；
*/
```
有关该视图各字段的详细说明：[[15_OceanBase/99_内部表介绍/MySql 租户权限相关_01#3 mysql.default_roles\|MySql 租户权限相关_01#3 mysql.default_roles]]，；


##### 2.3 查看角色与用户之间的授权关系

```sql
CREATE ROLE IF NOT EXISTS role1;                    -- 新建角色 `role1`
GRANT ALTER SYSTEM ON *.* TO role1;                 -- 将 `ALTER SYSTEM` 权限授予角色 `role1`
CREATE USER 'user001'@'%' IDENTIFIED BY '******';   -- 新建用户 `user001`
GRANT role001 TO user001 WITH ADMIN OPTION;         -- 将角色 `role1` 授予新创建的用户 `user001`

-- 查询角色 role1 和用户 user001 之间的授予关系
obclient [oceanbase]> SELECT * FROM mysql.role_edges;
+-----------+-----------+---------+---------+-------------------+
| FROM_HOST | FROM_USER | TO_HOST | TO_USER | WITH_ADMIN_OPTION |
+-----------+-----------+---------+---------+-------------------+
| %         | role001   | %       | user001 | Y                 |
+-----------+-----------+---------+---------+-------------------+
1 row in set (0.001 sec)
	-- 表示 `role001` 角色将其所有权限授予了 `user001` 用户，同时允许 `user001` 用户将该角色转授给其他用户或角色
/*
字段说明：
	FROM_HOST：varchar(255)，NO，被授予的 Role 账户 Host 名字；
	FROM_USER：varchar(128)，NO，被授予的 Role 账户名；
	TO_HOST：varchar(255)，NO，受赠者账户 Host 名字；
	TO_USER：varchar(128)，NO，受赠者账户名；
	WITH_ADMIN_OPTION：varchar(1)，NO，授予角色的权限是否具有 ADMIN 权限选项，即是否有权限将该角色转授给其他用户或角色；
*/
```
有关该视图各字段的详细说明：[[15_OceanBase/99_内部表介绍/MySql 租户权限相关_01#4 mysql.role_edges\|MySql 租户权限相关_01#4 mysql.role_edges]]，；

### 参考文档



