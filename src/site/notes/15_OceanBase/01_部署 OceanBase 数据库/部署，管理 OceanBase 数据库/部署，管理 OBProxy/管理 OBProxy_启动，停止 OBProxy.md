---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/管理 OBProxy_启动，停止 OBProxy/","dgPassFrontmatter":true}
---


## 管理 OBProxy

### 1 启动 OBProxy
#### 1.1 前提条件
请确认已部署 OBProxy，部署 OBProxy 的具体操作请参见 [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/部署 OBProxy_命令行\|部署 OBProxy_命令行]]，；

#### 1.2 启动方式
支持通过以下两种方式来启动 obproxy 进程：
1. 在启动命令中指定 `-r` 参数来指定 OceanBase 集群的 RootServer 信息，该启动方式不需要额外配置，一般用于开发调试阶段；
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
ps -ef|grep obproxy   # 检查进程是否存在
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

