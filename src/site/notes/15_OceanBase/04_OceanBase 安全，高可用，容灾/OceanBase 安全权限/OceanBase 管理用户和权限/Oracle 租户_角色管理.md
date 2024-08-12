---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/Oracle 租户_角色管理/","dgPassFrontmatter":true}
---


### Oracle 租户_角色管理
#### 1 角色管理概述  
OceanBase 数据库支持 Oracle 模式下的用户角色管理功能；在 Oracle 模式中，角色是一组系统权限和对象权限的组合；  

> [!NOTE] 在 OceanBase 数据库中，角色具备以下功能：  
> 1. 角色可以被授予系统权限或对象权限；  
> 2. 角色可以被授予其他角色，即角色中可以包含其他角色；  
> 3. 一个用户可以被授予多个角色，一个角色也可以授予多个用户；  
  
当前，OceanBase 数据库中默认有以下系统角色：CONNECT 角色，RESOURCE 角色，DBA 角色，PUBLIC 角色；  
*CONNECT 角色*：该角色提供了 CREATE SESSION 系统权限。如果您需要授予某个用户 CREATE SESSION 系统权限，除了直接授予该权限，您也可以通过授予用户 CONNECT 角色的方式来实现；  
*RESOURCE 角色*：该角色提供了以下系统权限：CREATE CLUSTER、CREATE INDEXTYPE、CREATE OPERATOR、CREATE PROCEDURE、CREATE SEQUENCE、CREATE TABLE、CREATE TRIGGER 和 CREATE TYPE；  

您可以通过 DBA_SYS_PRIVS 字典视图查看该角色所包含的权限；  
*DBA 角色*：该角色拥有非常强大的权限，提供了大量的系统权限，例如： DELETE ANY TABLE 和 GRANT ANY PRIVILEGE 等权限。您可以通过 DBA_SYS_PRIVS 字典视图查看该角色所包含的权限；  
为了数据库安全，OceanBase 数据库建议您根据自身业务需求来合理授权，请勿随意将该角色赋予用户；  
*PUBLIC 角色*：该角色适用于整个租户内的用户。默认未授予权限。如果您给 PUBLIC 角色授予了权限，则系统会将权限添加到租户内的每个用户的权限域中；所有用户都可以立即执行该权限所授权的操作；  
为了保证数据库的安全，不建议您随意向 PUBLIC 角色中授予权限； 

#### 2 创建，删除角色  
可以通过 CREATE ROLE 语句新建角色。新建角色需要当前用户具备 CREATE ROLE 系统权限；新建角色详情：[新建角色_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221472)，[删除角色_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221477)，；

```sql  
CREATE ROLE role1; -- 新建角色 role1  
CREATE ROLE role2 IDENTIFIED BY ******; -- 新建角色 role2，指定密码为 ******  
```  

可以使用 DROP ROLE 语句删除不需要的角色。删除角色需要当前用户具有 DROP ANY ROLE 系统权限并且已被授予具有该角色的 WITH ADMIN OPTION 权限；删除角色时，系统会将角色从已授予该角色的所有用户和角色中撤销，并将其从数据库中删除；  

注意：删除角色操作不会影响已启用该角色的用户会话。但是，在角色被删除后，如果建立新的用户会话，新的用户会话中将无法再启用该角色；  

```sql  
-- 删除角色，语法：DROP ROLE role_name;  
DROP ROLE role1;  
```  

更多 DROP ROLE 语句的说明信息，请参见 [DROP ROLE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222088)，；  

#### 3 角色权限管理  
##### 3.1 查看角色  
查看角色详情请参考：[查看角色_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221476)，；  
管理员可以通过 DBA_ROLES 视图查看系统中的所有角色，该视图的字段及说明请参见 [DBA_ROLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219447)，；  
管理员可以通过 ROLE_SYS_PRIVS 视图查看某个角色所拥有的系统权限，该视图的字段及说明请参见 [ROLE_SYS_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219302)，；  

```sql  
-- 查看当前系统中的角色  
SELECT * FROM DBA_ROLES;  
  
-- 查看角色所拥有的系统权限  
SELECT * FROM ROLE_SYS_PRIVS WHERE role='CONNECT'  
```  

拥有待查询角色的用户可以通过 ROLE_ROLE_PRIVS 视图查看角色中包含的角色，该视图的字段及说明请参见 [ROLE_ROLE_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219212)，；若该角色中不包含其他角色，则查询结果为空；  
拥有当前查询角色的用户可以通过 ROLE_TAB_PRIVS 视图查看角色所拥有的对象权限，该视图的字段请参见 [ROLE_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219197)，； 

```sql  
-- 查看角色中包含的角色  
SELECT * FROM ROLE_ROLE_PRIVS WHERE role='ROLE1';  
  
-- 查看角色所拥有的对象权限  
SELECT * FROM ROLE_TAB_PRIVS WHERE role='ROLE1';  
```

##### 3.2 将角色授予角色(或用户)  

将角色授予角色详情：[将角色授予角色_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221470)，，  
授予角色其他角色或用户时，当前用户必须拥有被授予的角色，并且拥有 ADMIN OPTION 权限，或者拥有 GRANT ANY ROLE 权限，才能授权成功；更多 GRANT 语句的说明信息，请参见 [GRANT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222051)，；  

```sql  
/*  
语法:  
	GRANT role_name1 TO role_name2 [WITH ADMID OPTION]; -- 将角色授予角色  
	GRANT role_name TO grantee_user [WITH ADMID OPTION]; -- 将角色授予用户  
参数：  
	role_name1：待授予其他角色的角色名。同时将多个角色授予角色时，角色名之间用英文逗号（,）分隔；
		一个角色可以同时授予多个角色；  
		多个角色也可以同时授予一个角色，并且多个角色也可以授予多个角色；  
	WITH ADMID OPTION：表示指定当前授予的角色允许转授，且取消授权时不会级联；  
*/  
GRANT role2 TO role1; -- 将角色 role2 授予角色 role1；  
GRANT role1 TO user2; -- 将角色 role1 授予用户 user2；  
```  


##### 3.3 修改角色权限  
修改角色权限详情：[修改角色_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221474)，；  
为角色授予系统权限、对象权限或角色时，当前用户必须拥有被授予的权限或角色，并且拥有 GRANT OPTION 权限，或者拥有 GRANT ANY OBJECT PRIVILEGE 权限、GRANT ANY PRIVILEGE 权限或 GRANT ANY ROLE 权限，才能授权成功；  

```sql  
GRANT SELECT ON test1.tbl1 TO role1; -- 将表 test1.tbl1 的 SELECT 对象权限授予角色 role1  
```  


##### 3.4 撤销角色  
撤销角色详情：[撤销角色_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221478)，；  
可以通过 REVOKE 语句将角色从用户或其他角色中撤销；撤销角色时，当前用户必须拥有被撤销的角色，并且拥有 GRANT OPTION 权限，或者拥有 GRANT ANY ROLE 权限，才能撤销成功；更多 REVOKE 语句的说明信息，请参见 [REVOKE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222036)，；

```sql  
/*  
语法：  
	REVOKE role_list FROM user_name; -- 撤销用户中的角色  
	REVOKE role_list FROM role_name; -- 撤销角色中的角色  
注意事项：  
	同时撤销多个角色时，角色之间用英文逗号（,）分隔；  
*/  
REVOKE role1 FROM test;  
```  

##### 3.5 为用户指定生效或失效的角色  
###### 3.5.1 指定用户在登录时生效或失效的角色  
OceanBase 数据库的 Oracle 模式支持通过 ALTER USER 语句的 DEFAULT ROLE 子句指定其他用户在登录时生效或失效的角色，更多 ALTER USER 语句的信息请参见 [ALTER USER](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222075)，；  

> [!NOTE] 前提条件：  
> 1. 当前用户必须拥有被授予的角色，并且拥有 GRANT OPTION 权限，才能执行生效或失效角色操作；  
> 2. 当前用户还必须拥有 UPDATE USER系统权限，才可以执行 ALTER USER 命令；  
  
```sql  
/*  
语法：  
	ALTER USER user_name DEFAULT ROLE role_name; -- 指定在登录时生效用户被授予的一个或多个角色  
	ALTER USER user_name DEFAULT ROLE ALL; -- 指定在登录时生效用户被授予的所有角色  
	ALTER USER user_name DEFAULT ROLE ALL EXCEPT role_name; -- 指定在登录时生效用户被授予的部分角色  
	ALTER USER user_name DEFAULT ROLE NONE; -- 指定在登录时失效用户被授予的所有角色  
语句使用说明：  
	role_name：角色名，多个角色名之间用英文逗号（,）分隔；  
	ALL：表示生效用户被授予的所有角色；  
	EXCEPT 子句表示除了 EXCEPT 子句中列出的角色除外，生效用户被授予的其他所有角色。多个角色名之间用英文逗号（,）分隔；  
	NONE：表示失效用户被授予的所有角色；  
*/  
ALTER USER test DEFAULT ROLE role1; -- 指定在登录时生效用户被授予的角色 role1  
```  

###### 3.5.2 指定当前会话中生效或失效的角色  
OceanBase 数据库的 Oracle 模式支持在当前会话中通过 SET ROLE 语句生效或失效当前登录用户被授予的角色；更多 SET ROLE 语句的信息请参见 [SET ROLE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222055)，；  

> [!NOTE] 说明：
> 通过 SET ROLE 语句生效或失效的角色，仅影响当前 Session，不影响之后的 Session；  

```sql  
/*  
语法：  
	SET ROLE role_name [ IDENTIFIED BY password ]; -- 指定在当前会话中生效当前登录用户被授予的一个或多个角色  
	SET ROLE ALL; -- 指定在当前会话中生效当前登录用户被授予的所有角色  
	SET ROLE ALL EXCEPT role_name; -- 指定在当前会话中生效当前登录用户被授予的部分角色  
	SET ROLE NONE; -- 指定在当前会话中失效当前登录用户被授予的所有角色  
语句使用说明：  
	role_name：角色名，多个角色名之间用英文逗号（,）分隔；  
	IDENTIFIED BY password：指定生效角色的密码。  
		如果在新建角色时，为该角色指定了密码，则您必须指定密码才能生效该角色；
	ALL：表示在当前会话中，生效当前登录用户被授予的所有角色；  
	EXCEPT 子句表示除了 EXCEPT 子句中列出的角色除外，生效当前登录用户被授予的其他所有角色。多个角色名之间用英文逗号（,）分隔。  
	NONE：表示失效当前登录用户被授予的所有角色  
*/  
SET ROLE role1 IDENTIFIED BY ******; -- 指定在当前会话中生效由密码 ****** 标识的角色 role1  
SET ROLE ALL EXCEPT role2; -- 指定在当前会话中生效除了角色 role2 之外的所有角色  
```


### 参考文档



