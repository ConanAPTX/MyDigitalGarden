---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库/","dgPassFrontmatter":true}
---


### 通过黑屏客户端连接 OB 数据库

#### 1 通过 MySQL 客户端连接数据库
 需要使用 OceanBase 数据库的 MySQL 租户时，可以使用 MySQL 客户端连接该租户；

##### 1.1 前提条件
 
> [!NOTE] 通过 MySQL 客户端连接数据库前，需要确认以下信息：
> 1. 确保本地已正确安装 MySQL 客户端。OceanBase 数据库当前版本支持的 MySQL 客户端版本包括 V5.5、V5.6 和 V5.7；
> 2. 确保环境变量 PATH 中包含了 MySQL 客户端命令所在目录；
> 3. 连接租户前，请确认当前客户端在租户白名单中，租户白名单的查询及设置具体操作请参见：[设置和查看租户白名单](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355590)，；

##### 1.2 通过 OBProxy 连接的方式

```sql
/*
语法：
	mysql -h10.10.10.1 -uusername@obmysql#obdemo -P2883 -ppassword -c -A oceanbase
	mysql -h10.10.10.1 -uobdemo:obmysql:username -P2883 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接 IP，通常是一个 OBProxy 地址。
	-u：提供租户的连接账户，
		格式有两种：【用户名@租户名#集群名】或者 【集群名:租户名:用户名】；
		格式有：【用户名@租户名#集群名】【集群名:租户名:用户名】【集群名-租户名-用户名】 或者 【集群名.租户名.用户名】
		使用 My SQL 客户端仅支持连接 MySQL 租户，MySQL 租户的管理员用户名默认是 root。
	-P：提供 OceanBase 数据库连接端口，也是 OBProxy 的监听端口，默认是 2883，可以自定义。
	-c：表示在 MySQL 运行环境中不要忽略注释；
		Hint 是特殊的注释，不受 -c 影响；
	-A：表示在 MySQL 连接数据库时不自动获取统计信息。
	oceanbase：访问的数据库的名称，可以更改为业务数据库
*/
-- 通过 MySQL 客户端连接 OceanBase 数据库的 MySQL 租户
	-- 先通过直连的方式连接 OceanBase 数据库；然后使用 `SHOW PARAMETERS LIKE 'cluster';` 命令来获取集群的名称；
	-- 如果 ODP 代理多个集群，连接时必须指定集群名；如果 ODP 只代理一个集群，连接时则不需指定集群名；
mysql -h 100.64.132.106 -P2883 -uroot@sys#zwy_hz_23011802 -p -A -c -Doceanbase  -- 需要输入密码，连接集群系统租户
mysql -h10.10.10.1 -u******@obmysql#obdemo -P2883 -p****** -c -A oceanbase      
mysql -h10.10.10.1 -uobdemo:obmysql:****** -P2883 -p****** -c -A oceanbase

MySQL [oceanbase]>    -- 连接成功后，默认会有命令行提示符
	-- 如果需要退出 MySQL 命令行，输入 exit 后回车，或者按快捷键 Ctrl + D；
```


##### 1.3 通过直连方式
普通租户通过直连方式连接时，需要确保该租户的资源分布在该 OBServer上，如果该租户的资源未分布在该 OBServer 上，则无法通过直连该 OBServer 连接到该租户；
 
```sql
/*
语法：
	mysql -h10.10.10.1 -uusername@obmysql -P2881 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接 IP，通常是一个 OBServer 的 IP 地址；
	-u：提供租户的连接账户，格式为：【用户名@租户名】；
	  使用 My SQL 客户端仅支持连接 MySQL 租户，MySQL 租户的管理员用户名默认是 root；
	-P：提供 OceanBase 数据库连接端口，默认是 2881，可以自定义；
	-p：提供账户密码，为了安全可以不提供，改为在后面提示符下输入，密码文本不可见；
	-c：表示在 MySQL 运行环境中不要忽略注释；
		Hint 是特殊的注释，不受 -c 影响；
	-A：表示在 MySQL 连接数据库时不自动获取统计信息；
	oceanbase：访问的数据库的名称，可以更改为业务数据库；
*/
-- 
mysql -h10.10.10.1 -uroot@obmysql -P2881 -p****** -c -A oceanbase

MySQL [oceanbase]>    -- 连接成功后，默认会有命令行提示符
	-- 如果需要退出 MySQL 命令行，输入 exit 后回车，或者按快捷键 Ctrl + D；
```


#### 2 通过 OBClient 连接 OceanBase 租户

 OBClient 是一个交互式和批处理查询工具，通过 OBClient 您可以连接 OceanBase 数据库的 MySQL 和 Oracle 租户；
 
##### 2.1 前提条件

> [!NOTE] 前提条件：
> 1. 请确认已下载并安装了 OBClient 应用；下载：[下载 OBClient](https://open.oceanbase.com/softwareCenter/community)，；
> 	1. rpm -qa | grep obclient # 查询是否安装 obclient 客户端
> 2. 连接租户前，请确认当前客户端在租户白名单中，租户白名单的查询及设置具体操作请参见：[设置和查看租户白名单](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355590)，；
 
##### 2.2 通过 OBProxy 连接的方式

```sql
/*
语法：
	obclient -h10.10.10.1 -uusername@obtenant#obdemo -P2883 -ppassword -c -A oceanbase
	obclient -h10.10.10.1 -uobdemo:obtenant:username -P2883 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接 IP，通常是一个 OBProxy 地址。
	-u：提供租户的连接账户，
		格式有两种：【用户名@租户名#集群名】 或者 【集群名:租户名:用户名】；
		格式有：【用户名@租户名#集群名】【集群名:租户名:用户名】【集群名-租户名-用户名】 或者 【集群名.租户名.用户名】
		MySQL 租户的管理员用户名默认是 root，Oracle 租户的管理员用户名默认是 sys；
	-P：提供 OceanBase 数据库连接端口，也是 OBProxy 的监听端口，默认是 2883，可以自定义；
	-c：表示在 MySQL 运行环境中不要忽略注释；
		Hint 是特殊的注释，不受 -c 影响；
	-A：表示在 MySQL 连接数据库时不自动获取统计信息；
	oceanbase：访问的数据库的名称，可以更改为业务数据库；
*/
rpm -qa | grep obclient -- 查询是否安装 obclient 客户端



-- 通过 OBClient 连接 OceanBase 数据库的 MySQL 租户
	-- 先通过直连的方式连接 OceanBase 数据库；然后使用 `SHOW PARAMETERS LIKE 'cluster';` 命令来获取集群的名称；
	-- 如果 ODP 代理多个集群，连接时必须指定集群名；如果 ODP 只代理一个集群，连接时则不需指定集群名；
obclient -h10.10.10.1 -u*****@obtenant#obdemo -P2883 -p****** -c -A sys        	
obclient -h10.10.10.1 -u*****@obtenant#obdemo -P2883 -p****** -c -A oceanbase
obclient -h10.10.10.1 -uobdemo:obtenant:***** -P2883 -p****** -c -A oceanbase

obclient>  -- 连接成功后，默认会有如下命令行提示符
	-- 如果需要退出 OBClient 命令行，输入 exit 后回车，或者按快捷键 Ctrl + D；
```


##### 2.3 通过直连方式
 普通租户通过直连方式连接时，需要确保该租户的资源分布在该 OBServer 上，如果该租户的资源未分布在该 OBServer 上，则无法通过直连该 OBServer 连接到该租户；

```sql
/*
语法：
	obclient -h10.10.10.1 -uusername@obtenant -P2881 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接IP，通常是一个 OBServer 的 IP 地址；
	-u：提供租户的连接账户，格式：【用户名@租户名】；
		MySQL 租户的管理员用户名默认是 root；Oracle 租户的管理员用户名默认是 sys；
	-P：提供 OceanBase 数据库连接端口，默认是 2881，可以自定义。
	-p：提供账户密码，为了安全可以不提供，改为在后面提示符下输入，密码文本不可见。
	-c：表示在 MySQL 运行环境中不要忽略注释；
		Hint 是特殊的注释，不受 -c 影响；
	-A：表示在 MySQL 连接数据库时不自动获取统计信息。
	oceanbase：访问的数据库的名称，可以更改为业务数据库。
*/
-- 通过 OBClient 连接 OceanBase 数据库的 Oracle 租户
	-- 在使用直连方式连接 OceanBase 数据库时，连接参数 -u 后应填写正确的 用户名@租户名，不要指定集群名。如果在 -u 中包含了集群名，会导致连接报错；
obclient -h10.10.10.1 -u******@obtenant -P2881 -p****** -c -A oceanbase
obclient -h10.10.10.1 -u******@t_oracle0_91#obdoc -P2883 -p****** -c -A sys  

obclient>  -- 连接成功后，默认会有如下命令行提示符;
	-- 如果需要退出 OBClient 命令行，输入 exit 后回车，或者按快捷键 Ctrl + D；
```


### 2 参考文档
1. *通过 MySQL 客户端连接 OceanBase 租户*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818875)，；
2. *通过 OBClient 连接 OceanBase 租户*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818872)，；

