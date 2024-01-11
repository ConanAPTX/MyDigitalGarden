---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/连接 OceanBase 数据库/","dgPassFrontmatter":true}
---


### 连接 OceanBase 数据库

#### 1 通过 OBClient 连接数据库
 OBClient 是一个交互式和批处理查询工具，通过 OBClient 您可以连接 OceanBase 数据库的 MySQL 和 Oracle 租户；
 
##### 1.1 前提条件
 1.请确认已下载并安装了 OBClient 应用；
 2.连接租户前，请确认当前客户端在租户白名单中，租户白名单的查询及设置具体操作请参见：[设置和查看租户白名单](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355590)，；
 
##### 1.2 通过 OBProxy 连接的方式

```text
语法：
	obclient -h10.10.10.1 -uusername@obtenant#obdemo -P2883 -ppassword -c -A oceanbase
	obclient -h10.10.10.1 -uobdemo:obtenant:username -P2883 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接 IP，通常是一个 OBProxy 地址。
	-u：提供租户的连接账户，格式有两种：【用户名@租户名#集群名】 或者 【集群名:租户名:用户名】；
	MySQL 租户的管理员用户名默认是 root，Oracle 租户的管理员用户名默认是 sys；
	-P：提供 OceanBase 数据库连接端口，也是 OBProxy 的监听端口，默认是 2883，可以自定义；
	-c：表示在 MySQL 运行环境中不要忽略注释；
	-A：表示在 MySQL 连接数据库时不自动获取统计信息；
	oceanbase：访问的数据库的名称，可以更改为业务数据库；
```

```sql
obclient -h10.10.10.1 -u*****@obtenant#obdemo -P2883 -p****** -c -A sys        
	-- 通过 OBClient 连接 OceanBase 数据库的 MySQL 租户
obclient -h10.10.10.1 -uobdemo:obtenant:***** -P2883 -p****** -c -A oceanbase
```


##### 1.3 通过直连方式
 普通租户通过直连方式连接时，需要确保该租户的资源分布在该 OBServer 上，如果该租户的资源未分布在该 OBServer 上，则无法通过直连该 OBServer 连接到该租户；

```text
语法：
	obclient -h10.10.10.1 -uusername@obtenant -P2881 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接IP，通常是一个 OBServer 的 IP 地址；
	-u：提供租户的连接账户，格式：【用户名@租户名】；
	MySQL 租户的管理员用户名默认是 root，Oracle 租户的管理员用户名默认是 sys。
	-P：提供 OceanBase 数据库连接端口，默认是 2881，可以自定义。
	-p：提供账户密码，为了安全可以不提供，改为在后面提示符下输入，密码文本不可见。
	-c：表示在 MySQL 运行环境中不要忽略注释。
	-A：表示在 MySQL 连接数据库时不自动获取统计信息。
	oceanbase：访问的数据库的名称，可以更改为业务数据库。
```

```sql
obclient -h10.10.10.1 -u******@obtenant -P2881 -p****** -c -A oceanbase
obclient -h10.10.10.1 -u******@t_oracle0_91#obdoc -P2883 -p****** -c -A sys  
	-- 通过 OBClient 连接 OceanBase 数据库的 Oracle 租户
```


#### 2 通过 MySQL 客户端连接数据库
 需要使用 OceanBase 数据库的 MySQL 租户时，可以使用 MySQL 客户端连接该租户；

##### 2.1 前提条件
 通过 MySQL 客户端连接数据库前，需要确认以下信息：
 确保本地已正确安装 MySQL 客户端。OceanBase 数据库当前版本支持的 MySQL 客户端版本包括 V5.5、V5.6 和 V5.7；
 确保环境变量 PATH 中包含了 MySQL 客户端命令所在目录；
 连接租户前，请确认当前客户端在租户白名单中，租户白名单的查询及设置具体操作请参见：[设置和查看租户白名单](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355590)，；

##### 2.2 通过 OBProxy 连接的方式

```text
语法：
	mysql -h10.10.10.1 -uusername@obmysql#obdemo -P2883 -ppassword -c -A oceanbase
	mysql -h10.10.10.1 -uobdemo:obmysql:username -P2883 -ppassword -c -A oceanbase
参数说明：
	-h：提供 OceanBase 数据库连接 IP，通常是一个 OBProxy 地址。
	-u：提供租户的连接账户，格式有两种：【用户名@租户名#集群名】或者 【集群名:租户名:用户名】；
	使用 My SQL 客户端仅支持连接 MySQL 租户，MySQL 租户的管理员用户名默认是 root。
	-P：提供 OceanBase 数据库连接端口，也是 OBProxy 的监听端口，默认是 2883，可以自定义。
	-c：表示在 MySQL 运行环境中不要忽略注释。
	-A：表示在 MySQL 连接数据库时不自动获取统计信息。
	oceanbase：访问的数据库的名称，可以更改为业务数据库
```

```sql
mysql -h 100.64.132.106 -P2883 -uroot@sys#zwy_hz_23011802 -p -A -c -Doceanbase
	-- 需要输入密码，连接集群系统租户
mysql -h10.10.10.1 -u******@obmysql#obdemo -P2883 -p****** -c -A oceanbase      
	-- 通过 MySQL 客户端连接 OceanBase 数据库的 MySQL 租户
mysql -h10.10.10.1 -uobdemo:obmysql:****** -P2883 -p****** -c -A oceanbase
```


##### 2.3 通过直连方式
普通租户通过直连方式连接时，需要确保该租户的资源分布在该 OBServer上，如果该租户的资源未分布在该 OBServer 上，则无法通过直连该 OBServer 连接到该租户；
 
```text
语法：
  mysql -h10.10.10.1 -uusername@obmysql -P2881 -ppassword -c -A oceanbase
 参数说明：
  -h：提供 OceanBase 数据库连接 IP，通常是一个 OBServer 的 IP 地址；
  -u：提供租户的连接账户，格式为：【用户名@租户名】；
   使用 My SQL 客户端仅支持连接 MySQL 租户，MySQL 租户的管理员用户名默认是 root；
  -P：提供 OceanBase 数据库连接端口，默认是 2881，可以自定义；
  -p：提供账户密码，为了安全可以不提供，改为在后面提示符下输入，密码文本不可见；
  -c：表示在 MySQL 运行环境中不要忽略注释；
  -A：表示在 MySQL 连接数据库时不自动获取统计信息；
  oceanbase：访问的数据库的名称，可以更改为业务数据库；
```

```sql
mysql -h10.10.10.1 -u******@obmysql -P2881 -p****** -c -A oceanbase
```


#### 3 通过 ODC 连接数据库
 可以通过 OceanBase 提供的开发者工具 ODC 来连接 OceanBase 数据库的 Oracle 和 MySQL 租户；
 详细情况请参考：[通过 ODC 连接数据库_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355644)，；

### 参考文档




