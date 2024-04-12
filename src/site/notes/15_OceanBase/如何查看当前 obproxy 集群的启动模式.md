---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/如何查看当前 obproxy 集群的启动模式/","dgPassFrontmatter":true}
---


### 如何查看当前 obproxy 集群的启动模式
obProxy 集群的启动方式有 2 种：`ConfigServer 启动模式`，`ConfigUrl 启动模式`；

`ConfigServer 启动模式`：无需依赖，一般用于测试环境；RsList 启动：直接指定单个 OB 集群 RsList IP (RootServer 的 IP 列表) 的启动方式，OBProxy 可以通过该 RsList 获取到整个集群元信息；这种方式*只能访问单个 OB 集群且不可更改*；

`ConfigUrl 启动模式`：一般用于生产模式；指定 ConfigUrl 作为启动参数的启动方式，OBProxy 根据用户连接串中的集群名从 ConfigUrl 获取对应集群的 RsList，然后根据该 RsList 获取到整个集群元信息；这种方式*可以访问多个 OB 集群*；


#### 1 如何查看当前 obproxy 集群的启动模式
如何查询 *obproxy* 集群的启动模式，可以通过 OCP，还可以通过默认的 2883 端口连接到 obproxy 中去，检查下面两个配置项，若哪个参数不为空，则为该启动模式；

```sql
-- 查询当前 obproxy 集群的启动模式
show proxyconfig like 'obproxy_config_server_url';   -- 若有值，则为 ConfigUrl 启动模式
show proxyconfig like 'rootservice_list';     -- 若有值，则为 ConfigServer 启动模式
```


#### 2 查看 obproxy 版本

ODP 安装后，有以下 3 种方法可以确定 ODP 的版本；

```sql
-- 1.方法 1
	-- 新版本 ODP 的目录会带版本号，如 `obproxy-4.0.0` 目录名表示 4.0.0 版本的 ODP；

-- 2.方法 2
	-- 在 ODP 的 home 目录下执行 `./bin/obproxy -V` 可查看该 ODP 的版本，以 V3.2.3 为例：
    [admin@test obproxy]# ./bin/obproxy -V
    ./bin/obproxy -V
    obproxy (OceanBase 3.2.3 2)
    REVISION: 6-local-99faebfc7130b70ad0f56330a28cab6a32ec9a33
    BUILD_TIME: Mar 30 2022 01:53:08

-- 3.方法 3
	-- 使用 `root@proxysys` 账号登录 ODP 后，执行 `show proxyinfo binary` 可查看该 ODP 的版本，以 V4.0.0 为例：
	MySQL [(none)]> show proxyinfo binary\G
	*************************** 1. row ***************************
	name: binary info
	info: ObProxy-OceanBase 4.0.0-20221103165243.el7
	version:RELEASE_7U
	MD5:
	REVISION:20221103165243-7c7e5821009b2d3924d1bc7a8165edf7384f6ed3
	BUILD_TIME:Nov  3 2022 16:57:16
```


### 参考文档
1. [ODP 基本操作](https://www.oceanbase.com/docs/enterprise-odp-enterprise-cn-10000000001330430)，查询 obproxy 版本，重启 ODP，ODP 目录介绍；


