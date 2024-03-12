---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理 DBLink_Oracle 模式/","dgPassFrontmatter":true}
---


### 管理 DBLink_Oracle 模式
#### 1 查看已创建的 DBLink  
可以通过 `sys.all_virtual_dblink_agent` 表来查看已创建的 DBLink；  

```sql
-- 查询 DBLink  
SELECT * FROM sys.all_virtual_dblink_agent;  
```


#### 2 通过 DBLink 访问远端数据库中的数据  
可以通过 SELECT... FROM 表名@DBLink 名 来查询远端数据库中的数据，当前暂不支持通过 DBLink 修改、插入或删除数据；  

```sql
/*  
语法：SELECT select_expr_list FROM table_name@dblink_name WHERE where_conditions;  
参数：  
	table_name@dblink_name：指定待查询的远端数据库的表，多个数据库之间用英文逗号（,）隔开；  
	where_conditions：指定筛选条件，查询结果中仅包含满足条件的数据，为可选项；  
*/  
  
-- 通过名为 my_link 的 DBLink 查询远端数据库中表 tbl1 的数据  
SELECT * FROM tbl1@my_link;  
```

  
#### 3 创建 DBLink  
执行该语句的用户需要具备 CREATE DATABASE LINK 权限；  

##### 3.1 创建 OceanBase 数据库到 OceanBase 数据库的 DBLink  

```sql
/* 语法：CREATE DATABASE LINK dblink_name CONNECT TO user@tenant IDENTIFIED BY {$password} [OB] HOST 'ip:port' [CLUSTER cluster_name];  
参数：  
	dblink_name：DBLink 的名称，长度不超过 128 个字符；  
	user：远端 OceanBase 数据库的用户名；  
	tenant：远端 OceanBase 数据库的租户名。当前仅支持 Oracle 租户；  
	$password：远端 OceanBase 数据库用户名的登录密码；  
	OB：表示指定访问的远端数据库的类型为 OceanBase。若不指定该参数，即默认为 OceanBase；  
	ip:port：IP地址与端口；  
	cluster_name：远端 OceanBase 数据库对应的集群的名称；  
*/  
  
-- 创建连接到远端 OceanBase 数据库的 DBLink，IP 地址和端口号为指定 OBServer 的 IP 地址和端口号  
CREATE DATABASE LINK ob_dblink CONNECT TO ob_user@oracle IDENTIFIED BY *** OB HOST '10.10.10.1:12345';  
-- 创建连接到远端 OceanBase 数据库的 DBLink，IP 地址和端口号为集群的 Proxy 的 IP 地址和端口号，同时 Proxy 由 ConfigUrl 方式部署  
CREATE DATABASE LINK ob_dblink_proxy CONNECT TO ob_user@oracle IDENTIFIED BY *** OB HOST '10.10.10.2:30084' CLUSTER "ob323";  
```

  
##### 3.2 创建 OceanBase 数据库到 Oracle 数据库的 DBLink  

```sql
/* 语法：CREATE DATABASE LINK dblink_name CONNECT TO user@oracle IDENTIFIED BY {$password} OCI HOST 'ip:port/oracle_sid';  
参数：  
	dblink_name：DBLink 的名称，长度不超过 128 个字符；  
	user：远端 Oracle 数据库的用户名。  
	oracle：连接 Oracle 数据库时，该值始终为 oracle。  
	$password：远端 Oracle 数据库用户名的登录密码。  
	OCI：表示指定访问的远端数据库的类型为 Oracle。  
	oracle_sid：远端 Oracle 数据库的 SID  
*/  
  
-- 创建 OceanBase 数据库到 Oracle 数据库的 DBLink  
CREATE DATABASE LINK orcl_dblink CONNECT TO orcl_user@oracle IDENTIFIED BY *** OCI HOST '10.10.10.2:1521/ORCL';  
```


#### 4 删除 DBLink  

```sql
/* 语法：DROP DATABASE LINK dblink_name;  
参数：  
	dblink_name: 表示待删除的 DBLink 的名称；  
*/  
  
-- 删除 DBLink  
DROP DATABASE LINK ob_dblink;  
DROP DATABASE LINK orcl_dblink;
```



### 参考文档



