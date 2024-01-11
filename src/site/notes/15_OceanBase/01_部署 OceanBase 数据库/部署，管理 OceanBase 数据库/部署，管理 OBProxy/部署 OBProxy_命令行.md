---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/部署 OBProxy_命令行/","dgPassFrontmatter":true}
---


### 部署 OBProxy_命令行

> [!NOTE] 注意事项：
> 1. OBProxy 在任何一个机器上部署后，通过向外暴露一个 ip:port 来提供 OceanBase 的代理服务。用户可以像访问 MySQL 数据库一样通过该 ip:port 即可访问 OceanBase 数据库，一般推荐部署在 OBServer 节点上；
> 2. 建议一台机器上只部署一个 OBProxy 服务，并且使用约定的 2883 端口。当同一台机器上部署多个OBProxy 服务时，需要指定不同端口和不同的配置文件路径予以区分，端口可以自定义为 3306 或 1521、或者其他端口；


#### 1 安装 OBProxy

```shell
cd $rpm_dir                 # `$rpm_dir` 表示存放 RPM 包的目录

# 安装
sudo rpm -ivh $rpm_name     # `$rpm_name` 表示 RPM 包的名称
sudo rpm -ivh obproxy-3.2.3-20220310201314.el7.x86_64.rpm
	# OBProxy 安装目录在 /opt/taobao/install 下
```


##### 1.1 创建到 obproxy 的软链接
该操作是可选步骤，创建到 obproxy 的软链接可以隐藏掉版本信息，方便后期操作；
obproxy 安装目录在 /opt/taobao/install 下。
obproxy 进程 home 目录是 `/opt/taobao/install/obproxy`，这是一个软链接，实际指向相应的 obproxy 版本软件目录；

```shell
[root@xxx admin]# su - admin
-bash-4.2$ cd /opt/taobao/install
-bash-4.2$ sudo ln -s obproxy-3.2.3 obproxy
-bash-4.2$ ll
total 8
drwxr-xr-x 9 root  root  4096 Mar 22 10:24 ajdk-8.3.6-b129
lrwxrwxrwx 1 root  root    13 Apr 28 10:25 obproxy -> obproxy-3.2.3
drwxr-xr-x 4 admin admin 4096 Apr 27 17:56 obproxy-3.2.3

--把 obproxy 的所有者和所属组都改为 admin。
[root@xxx install]# chown -R admin:admin obproxy
[root@xxx install]# ll
total 8
drwxr-xr-x 9 root  root   4096 Mar 22 10:24 ajdk-8.3.6-b129
lrwxrwxrwx 1 admin admin    13 Apr 28 10:25 obproxy -> obproxy-3.2.3
drwxr-xr-x 4 admin admin  4096 Apr 27 17:56 obproxy-3.2.3
```


##### 1.2 建立 obproxy 进程运行日志目录
obproxy 进程的运行日志目录通过软链接指向 `/home/admin/logs/obproxy/log`；

```shell
[root@xxx admin]# su - admin
-bash-4.2$ mkdir -p /home/admin/logs/obproxy/log
-bash-4.2$ for t in {log};do ln -s /opt/taobao/install/obproxy/$t /home/admin/logs/obproxy/log/$t; done
-bash-4.2$ tree /opt/taobao/install/obproxy
/opt/taobao/install/obproxy
|-- bin
|   |-- obp_xflush.py
|   |-- obproxy
|   |-- obproxyd.sh
|   `-- unzip.py
|-- log -> /home/admin/logs/obproxy/log
|-- minidump -> /home/admin/logs/obproxy/minidump
|-- start_obproxy.sh
`-- tools
    |-- dump_syms
    |-- log4cplus.conf
    |-- minidump.sh
    |-- minidump_stackwalk
    `-- obproxy.sym

3 directories, 11 files
```

#### 2 初始化 OBProxy
##### 2.1 初始化 OBProxy 账户
OBProxy 需要跟后端 OBServer 节点保持通信。所以需要提前在 OceanBase 集群的 sys 租户下为 OBProxy 创建连接用户（proxyro）和密码。后续在启动 OBProxy 时可通过设置 proxy 启动参数的方式来告知 proxy；


> [!NOTE]  `注意：`
> 1. proxyro 用户是 OBProxy 访问 OceanBase 集群的用户。一个 OceanBase 集群对应一个 proxyro 账号；

```sql
$ obclient -h127.1 -uroot@sys -P2881 -p -c -A
Enter password: 

obclient> CREATE USER proxyro IDENTIFIED BY '******';
Query OK, 0 rows affected

obclient> GRANT SELECT ON *.* TO proxyro;
Query OK, 0 rows affected

obclient> SHOW GRANTS FOR proxyro;
+----------------------------------+
| Grants for proxyro@%             |
+----------------------------------+
| GRANT SELECT ON *.* TO 'proxyro' |
+----------------------------------+
1 row in set
```

##### 2.2 启动 OBProxy

> [!NOTE]  `注意：`
> 启动 OBProxy 时，请在 admin 用户下并在 OBProxy 软件的 home 目录。其他用户或者其他目录下启动都可能带来问题；


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/01-ocean-base/ocean-base/ob-proxy/ob-proxy-ob-proxy/#3" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





## 管理 OBProxy

### 1 启动 OBProxy
#### 1.1 前提条件
请确认已部署 OBProxy，部署 OBProxy 的具体操作请参见 [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/部署 OBProxy_命令行\|部署 OBProxy_命令行]]，；

#### 1.2 启动方式
支持通过以下两种方式来启动 obproxy 进程：
1. 在启动命令中指定 `-r` 参数来指定 OceanBase 集群的 RootServer 信息，该启动方式不需要额外配置，一般用于开发调试阶段；
	1. 
2. 在启动命令中指定 `obproxy_config_server_url` 参数项来查询获取 OceanBase 集群的 RootServer 信息，该方式需要配置 `obproxy_config_server_url`，故会依赖 Config Server 的启动。推荐使用该方式启动 OBProxy；

#### 1.3 启动步骤
```shell
# 1.使用 admin 用户登录到 obproxy 进程所在的机器
ssh admin@10.10.10.1

# 进入 OBProxy 的安装目录
cd /opt/taobao/install/obproxy
	# 启动 OBProxy 时，请使用 admin 用户登录并在 OBProxy 软件的 home 目录下执行启动命令。
	# 使用其他用户或者在其他目录下启动 OBProxy 都可能带来问题

# 查看 OBProxy 的启动参数有哪些
./bin/obproxy -h
```

##### 1.3.1 启动 OBProxy
###### 1.3.1.1 1.`启动方式_1：`
在启动命令中指定 `-r` 参数；
```shell
cd /opt/taobao/install/obproxy

# 启动 OBProxy
<<###
# 语法：
	./bin/obproxy -p6789 -r'ip:port' -e -n appname -o obproxy_config_server_url='' -c cluster_name
	.bin/obproxy -r "10.10.10.1:2881;10.10.10.2:2881;10.10.10.3:2881" -p 2883 -o "observer_sys_password=$sha1_value,enable_strict_kernel_release=false,enable_cluster_checkout=false,enable_metadb_used=false" -c obdemo
参数详解：
-p：用于指定 OBProxy 监听的端口号，客户端通过 MySQL 连接该端口访问 OceanBase 数据库。仅在第一次启动时需要指定该参数，后续续日常启动或升级等均不需要添加此参数；
-p：指定服务端口号，一般指定为 2883；
-r：指定的 OceanBase 集群的 RootServer 信息，包括 IP 地址和端口号信息。该 Port 指的是 OBServer 的 SQL Port，不是 RPC Port
-e：用于指定的建表操作，建议仅在 OBProxy 第一次启动时进行，后续日常启动或升级等均不需要添加此参数
-c：指定集群名称；
-o：指定硬件或者内核参数配置。
	如果不指定，则使用系统默认配置。obproxy_config_server_url='' 表示无需 Config Server 启动；
	$sha1_value：应根据实际设置密码替换。设置 proxy 启动参数方式，设置的密码是 sha1 后的值，而不是原始值。
		例如：proxyro 用户设置的密码是 `123456`，则需要设置 `observer_sys_password` 的值是 `7c4a8d09ca3762af61e59520943dc26494f8941b`；
-n：可选参数，用于指定待启动的 OBProxy 的应用名。OBProxy 的应用名可以通过 `app_name` 配置项来修改，默认为 undefined；
###
$./bin/obproxy -r'10.10.10.1:26506;10.10.10.2:26506' -n test -c mycluster
```

###### 1.3.1.2 2.`启动方式_2：`
在启动命令中指定 obproxy_config_server_url 参数
```shell
cd /opt/taobao/install/obproxy

# 启动 OBProxy
# 语法：./bin/obproxy -p6789 -e -n appname -o obproxy_config_server_url='your_config_url'
<<#####
-p: 用于指定 OBProxy 监听的端口号，客户端通过 MySQL 连接该端口访问 OceanBase 数据库。仅在第一次启动时需要指定该参数，后续续日常启动或升级等均不需要添加此参数。
-e: 用于指定的建表操作，建议仅在 OBProxy 第一次启动时进行，后续日常启动或升级等均不需要添加此参数。
-n：用于指定应用名。OBProxy 的应用名可以通过 `app_name` 配置项来修改，默认为 `undefined`。
-o: 用于指定硬件或者内核参数配置。如果不指定，则使用系统默认配置。
	obproxy_config_server_url: 用于指定 Config Server 的访问地址
#####
./bin/obproxy -n test -o obproxy_config_server_url='http://xx.xx.xx.xx:8877/obproxy_config'
```


#### 1.4 查看 obproxy 进程是否存在

```shell
# 检查进程是否存在
ps -ef|grep obproxy
```



### 2 停止 OBProxy
```shell
# 1.使用 admin 用户登录到 obproxy 进程所在的机器
ssh admin@10.10.10.1

# 2. 查看 obproxy 进程的进程号
ps -ef|grep obproxy  # 检查进程是否存在
kill -9 37360        # 停止 OBProxy 进程

# 3. 检查进程是否存在
ps -ef|grep obproxy  
```

### 3 参考文档



</div></div>


```shell
# 再次启动 OBProxy 进程时就不需要指定那么参数。因为参数已经写到参数文件里
cd /opt/taobao/install/obproxy && bin/obproxy

# 查询日志
# OBProxy 的运行日志在 `/opt/taobao/install/obproxy/log` 下，也就是 `/home/admin/logs/obproxy/log` 下
```


##### 2.3 停止 OBProxy

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/01-ocean-base/ocean-base/ob-proxy/ob-proxy-ob-proxy/#2-ob-proxy" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





## 管理 OBProxy

### 1 启动 OBProxy
#### 1.1 前提条件
请确认已部署 OBProxy，部署 OBProxy 的具体操作请参见 [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/部署 OBProxy_命令行\|部署 OBProxy_命令行]]，；

#### 1.2 启动方式
支持通过以下两种方式来启动 obproxy 进程：
1. 在启动命令中指定 `-r` 参数来指定 OceanBase 集群的 RootServer 信息，该启动方式不需要额外配置，一般用于开发调试阶段；
	1. 
2. 在启动命令中指定 `obproxy_config_server_url` 参数项来查询获取 OceanBase 集群的 RootServer 信息，该方式需要配置 `obproxy_config_server_url`，故会依赖 Config Server 的启动。推荐使用该方式启动 OBProxy；

#### 1.3 启动步骤
```shell
# 1.使用 admin 用户登录到 obproxy 进程所在的机器
ssh admin@10.10.10.1

# 进入 OBProxy 的安装目录
cd /opt/taobao/install/obproxy
	# 启动 OBProxy 时，请使用 admin 用户登录并在 OBProxy 软件的 home 目录下执行启动命令。
	# 使用其他用户或者在其他目录下启动 OBProxy 都可能带来问题

# 查看 OBProxy 的启动参数有哪些
./bin/obproxy -h
```

##### 1.3.1 启动 OBProxy
###### 1.3.1.1 1.`启动方式_1：`
在启动命令中指定 `-r` 参数；
```shell
cd /opt/taobao/install/obproxy

# 启动 OBProxy
<<###
# 语法：
	./bin/obproxy -p6789 -r'ip:port' -e -n appname -o obproxy_config_server_url='' -c cluster_name
	.bin/obproxy -r "10.10.10.1:2881;10.10.10.2:2881;10.10.10.3:2881" -p 2883 -o "observer_sys_password=$sha1_value,enable_strict_kernel_release=false,enable_cluster_checkout=false,enable_metadb_used=false" -c obdemo
参数详解：
-p：用于指定 OBProxy 监听的端口号，客户端通过 MySQL 连接该端口访问 OceanBase 数据库。仅在第一次启动时需要指定该参数，后续续日常启动或升级等均不需要添加此参数；
-p：指定服务端口号，一般指定为 2883；
-r：指定的 OceanBase 集群的 RootServer 信息，包括 IP 地址和端口号信息。该 Port 指的是 OBServer 的 SQL Port，不是 RPC Port
-e：用于指定的建表操作，建议仅在 OBProxy 第一次启动时进行，后续日常启动或升级等均不需要添加此参数
-c：指定集群名称；
-o：指定硬件或者内核参数配置。
	如果不指定，则使用系统默认配置。obproxy_config_server_url='' 表示无需 Config Server 启动；
	$sha1_value：应根据实际设置密码替换。设置 proxy 启动参数方式，设置的密码是 sha1 后的值，而不是原始值。
		例如：proxyro 用户设置的密码是 `123456`，则需要设置 `observer_sys_password` 的值是 `7c4a8d09ca3762af61e59520943dc26494f8941b`；
-n：可选参数，用于指定待启动的 OBProxy 的应用名。OBProxy 的应用名可以通过 `app_name` 配置项来修改，默认为 undefined；
###
$./bin/obproxy -r'10.10.10.1:26506;10.10.10.2:26506' -n test -c mycluster
```

###### 1.3.1.2 2.`启动方式_2：`
在启动命令中指定 obproxy_config_server_url 参数
```shell
cd /opt/taobao/install/obproxy

# 启动 OBProxy
# 语法：./bin/obproxy -p6789 -e -n appname -o obproxy_config_server_url='your_config_url'
<<#####
-p: 用于指定 OBProxy 监听的端口号，客户端通过 MySQL 连接该端口访问 OceanBase 数据库。仅在第一次启动时需要指定该参数，后续续日常启动或升级等均不需要添加此参数。
-e: 用于指定的建表操作，建议仅在 OBProxy 第一次启动时进行，后续日常启动或升级等均不需要添加此参数。
-n：用于指定应用名。OBProxy 的应用名可以通过 `app_name` 配置项来修改，默认为 `undefined`。
-o: 用于指定硬件或者内核参数配置。如果不指定，则使用系统默认配置。
	obproxy_config_server_url: 用于指定 Config Server 的访问地址
#####
./bin/obproxy -n test -o obproxy_config_server_url='http://xx.xx.xx.xx:8877/obproxy_config'
```


#### 1.4 查看 obproxy 进程是否存在

```shell
# 检查进程是否存在
ps -ef|grep obproxy
```



### 2 停止 OBProxy
```shell
# 1.使用 admin 用户登录到 obproxy 进程所在的机器
ssh admin@10.10.10.1

# 2. 查看 obproxy 进程的进程号
ps -ef|grep obproxy  # 检查进程是否存在
kill -9 37360        # 停止 OBProxy 进程

# 3. 检查进程是否存在
ps -ef|grep obproxy  
```

### 3 参考文档



</div></div>


#### 3 OceanBase 数据库连接
通过 OBProxy 连接时，用户名的格式需要包含用户名、租户名和集群名。格式为： `用户名@租户名#集群名` 或 `集群名:租户名:用户名`；
```shell
obclient -h10.10.10.1 -uroot@sys#obdemo -P2883 -p -c -A oceanbase    
obclient -h10.10.10.1 -uobdemo:sys:root -P2883 -p -c -A oceanbase
```



### 参考文档：
