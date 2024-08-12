---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 部署类型/","dgPassFrontmatter":true}
---


### OMS 部署类型
#### 1 单节点部署
OMS 的单个节点可以提供全部 OMS 的能力。在部署 OMS 时，如果您无需使用高可用环境，可以选择单节点部署。详情请参见 [单节点部署](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613365)，；

#### 2 单地域多节点部署
OMS 支持通过多节点部署来实现高可用。在高可用架构中，OMS 的每个节点均具备完整功能，后台任务框架基于数据库实现了分布式调度。当某个 OMS 节点不可用时，框架会自动将任务调度到可用的 OMS 节点；

同时，OMS 支持在单个地域部署多节点。详情请参见 [单地域多节点部署](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613367)，；

#### 3 多地域多节点部署
OMS 支持多地域部署，您可以在多个地域部署 OMS。详情请参见 [多地域多节点部署](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613362)，；


#### 4 基于 OAT 部署
如果环境中安装了 OceanBase 管理工具（OceanBase Admin Toolkit，OAT），OMS 支持通过 OAT 3.2.0 及之后版本在部署服务器上安装 OMS，详情请参见 OAT 的官方文档；

##### 4.1 注意事项
1. 使用 OMS 2.1.0 及之后版本时，如果您需要 OMS 收集和展示历史监控数据，请部署 InfluxDB 时序数据库；
2. OMS 默认关闭高可用（HA）功能。如果您需要开启，可以通过以下两种方式：
	1. OMS 3.1.0 之前的版本，通过执行命令的方式启动 HA；
	2. OMS 3.1.0 及之后版本，通过在 OMS 管理控制台修改 `ha.config` 参数中 `enable` 的取值为 `true` 的方式启动 HA。详情请参见 [修改高可用功能的配置](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613415)，；
```shell
# OMS 3.1.0 之前的版本，通过执行命令的方式启动 HA
supervisorctl start oms_drc_ha
```

### 参考文档
1. [OMS 部署类型_V4.2.2](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613366)，；


