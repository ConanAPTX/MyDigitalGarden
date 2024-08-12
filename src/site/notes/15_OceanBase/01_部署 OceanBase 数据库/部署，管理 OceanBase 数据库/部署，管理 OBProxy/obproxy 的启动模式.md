---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/obproxy 的启动模式/","dgPassFrontmatter":true}
---


### obproxy 的启动模式

#### 1 OBProxy 启动模式
OBProxy 两种启动模式：*测试模式*，*生产模式*；

`测试模式`：主要用于现阶段开发调试，无需依赖 ConfigServer；
ConfigServer 是 OCP 平台提供的 OB 集群物理入口管理服务，是一个 web api 的服务；
测试模式通过指定集群的 RSList（ip 列表）来启动，OBProxy 集群仅可访问创建 OBProxy 集群时指定的那个 OceanBase 集群，OBProxy 集群创建成功后不可追加可连接的 OB 集群;
  
`生产模式`：
ObProxy 可以通过指定 config server 提供的 config_url 来启动，config server 服务可以协助获取该集群的配置信息。同一个 config server 可以保存多个 OB 集群的 RSList 信息，使 obproxy 能为多个 OB 集群同时提供服务；
在连接 ObProxy 时，其用户名类似 root@sys#cluster，其中 root 为用户名，sys 为租户名，cluster 为集群名;

#### 2 如何查看当前 obproxy 集群的启动模式
obProxy 集群的启动方式有 2 种：`ConfigServer 启动模式`，`ConfigUrl 启动模式`；

`ConfigServer 启动模式`：无需依赖，一般用于测试环境；RsList 启动：直接指定单个 OB 集群 RsList IP (RootServer 的 IP 列表) 的启动方式，OBProxy 可以通过该 RsList 获取到整个集群元信息；这种方式*只能访问单个 OB 集群且不可更改*；

`ConfigUrl 启动模式`：一般用于生产模式；指定 ConfigUrl 作为启动参数的启动方式，OBProxy 根据用户连接串中的集群名从 ConfigUrl 获取对应集群的 RsList，然后根据该 RsList 获取到整个集群元信息；这种方式*可以访问多个 OB 集群*；


##### 2.1 如何查看当前 obproxy 集群的启动模式
如何查询 *obproxy* 集群的启动模式，可以通过 OCP，还可以通过默认的 2883 端口连接到 obproxy 中去，检查下面两个配置项，若哪个参数不为空，则为该启动模式；

```sql
-- 查询当前 obproxy 集群的启动模式
show proxyconfig like 'obproxy_config_server_url';   -- 若有值，则为 ConfigUrl 启动模式
show proxyconfig like 'rootservice_list';            -- 若有值，则为 ConfigServer 启动模式
```


### 参考文档
1. [ODP 基本操作](https://www.oceanbase.com/docs/enterprise-odp-enterprise-cn-10000000001330430)，查询 obproxy 版本，重启 ODP，ODP 目录介绍；

