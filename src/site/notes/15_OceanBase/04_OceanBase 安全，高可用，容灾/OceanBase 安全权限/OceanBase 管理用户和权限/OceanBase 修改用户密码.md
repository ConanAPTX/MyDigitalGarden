---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/OceanBase 修改用户密码/","dgPassFrontmatter":true}
---

### OceanBase 修改用户密码
用户(租户)被创建后，管理员或用户自己可以修改密码；

#### 1 通过 ALTER USER 语句修改密码
ALTER USER 语句可以用于修改其他用户的密码。同时，当前用户必须拥有 UPDATE USER系统权限，才可以执行 ALTER USER 命令；

```sql
-- 修改用户密码_MySql 租户，Oracle 租户
	-- 语法：ALTER USER username IDENTIFIED BY password;
	-- 语法：ALTER USER username IDENTIFIED BY password;

obclient> ALTER USER sqluser01 IDENTIFIED BY 123456;  -- 将用户 sqluser01 的密码修改为 123456
obclient> ALTER USER sqluser01 IDENTIFIED BY 123456;  -- 将用户 sqluser01 的密码修改为 123456
```


#### 2 通过 SET PASSWORD 语句修改用户密码
```sql
SET PASSWORD 语句可以用于修改当前登录用户或其他用户的密码；

/*
语法：
 SET PASSWORD [FOR user] = PASSWORD(password);
语句使用说明：
 如果没有 For user 子句，表示修改当前用户的密码。任何成功登录的用户都可以修改当前用户的密码。
 如果有 For user 子句，表示修改指定用户的密码。当前用户必须拥有 CREATE USER 的系统权限，才可以修改指定用户的密码
*/

-- 将用户 sqluser01 的密码修改为 123456
obclient> SET PASSWORD for sqluser01 = PASSWORD(123456);
Query OK, 0 rows affected
```


#### 3 通过 OCP 修改用户密码
可以通过 OCP 修改用户的密码；
##### 3.1 前提条件
请确认当前 OCP 用户具备 TENANT_MANAGER 角色的权限，如果没有该角色权限，请联系 OCP 管理员为您添加相应角色权限，具体操作方法请参见 OCP 对应版本的《用户指南》文档中的 编辑用户；


### 参考文档
