---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/","dgPassFrontmatter":true}
---


### 部署 OceanBase 数据库
1. [[15_OceanBase/01_部署 OceanBase 数据库/OB 标准交付推荐版本包\|OB 标准交付推荐版本包]]，；

#### 1 部署，管理 OAT，OceanBase 管理者工具
1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/安装 OAT_使用 OAT 安装包安装\|安装 OAT_使用 OAT 安装包安装]]，使用 OAT 安装包安装；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/安装 OAT_使用 oat-all-in-one 安装包安装\|安装 OAT_使用 oat-all-in-one 安装包安装]]，【推荐使用】；
3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/管理者工具 OAT 升级指南_V3.2 升级至 V4.1\|管理者工具 OAT 升级指南_V3.2 升级至 V4.1]]，；
4. `产品服务`
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/OAT 安装，升级和卸载 OMS\|OAT 安装，升级和卸载 OMS]]，使用 OAT 安装，升级和卸载 OMS；

#### 2 部署，管理 OCP，OceanBase 开发者中心
1. 在 OAT 中安装单节点 OCP
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署安装 OCP 规划资源\|部署安装 OCP 规划资源]]，需要规划的资源包括：OCP 配置服务器要求、MetaDB 资源、MonitorDB 资源 和 主机资源；
	2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/准备部署环境\|准备部署环境]]，；
	3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署单点 OCP\|部署单点 OCP]]，；
	4. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署 OCP 后的检查\|部署 OCP 后的检查]]，；
2. 在 OAT 中安装高可用模式 OCP
3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/OCP 平台的用户管理\|OCP 平台的用户管理]]，；
4. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/获取 OCP 相关账号密码\|获取 OCP 相关账号密码]]，；


#### 3 部署，管理 OceanBase 数据库
##### 3.1 部署，管理 OceanBase 数据库
1. [快速上手](https://open.oceanbase.com/quickStart)，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/启动 observer 进程\|启动 observer 进程]]，；
3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/未命名 1\|15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/未命名 1]]，；

##### 3.2 部署 管理 OBProxy
1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/OBProxy 管理概述\|OBProxy 管理概述]]，；
2. `部署 OBProxy`
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/OBProxy 部署\|OBProxy 部署]]，；
	2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/部署 OBProxy_命令行\|部署 OBProxy_命令行]]，通过命令行的方式部署 obproxy，及查询 obproxy 版本；
	3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/生产环境运行 OBProxy - 守护进程\|生产环境运行 OBProxy - 守护进程]]，；
3. *管理 OBProxy*
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/管理 OBProxy 集群\|管理 OBProxy 集群]]，；
	2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/管理 OBProxy_启动，停止 OBProxy\|管理 OBProxy_启动，停止 OBProxy]]，；
	3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/obproxy 的启动模式\|obproxy 的启动模式]]，介绍 obproxy 启动模式概述及如何查询；
4. `OBProxy 参数调优`，详细情况请参考：[[15_OceanBase/05_OceanBase 性能调优#^9c71b0\|05_OceanBase 性能调优#^9c71b0]]，；



##### 3.3 部署，管理 ODC
1. `下载 ODC，OceanBase 开发者中心`
	1. [下载桌面版 ODC](https://help.aliyun.com/document_detail/212816.html)，可以下载各版本的桌面版 ODC 安装包；除此之外还可以在：[OceanBase 软件下载中心](https://www.oceanbase.com/softwarecenter-enterprise)，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 ODC 日志\|获取 ODC 日志]]，；

#### 4 OMS，OceanBase 迁移服务
1. `部署 OMS`
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 部署类型\|OMS 部署类型]]，；
	2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/部署 OMS 的前期准备\|部署 OMS 的前期准备]]，；
	3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/OAT 安装，升级和卸载 OMS\|OAT 安装，升级和卸载 OMS]]，使用 OAT 安装，升级和卸载 OMS；
	4. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/部署 OMS_单地域多节点\|部署 OMS_单地域多节点]]，；
	5. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/检查 OMS 部署状态\|检查 OMS 部署状态]]，检查 OMS 部署状态；
2. `升级 OMS`
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/黑屏升级 OMS 版本_1\|黑屏升级 OMS 版本_1]]，*多节点升级*，OMS 从 `V3.2.1 及之后，V4.0.1 之前版本`升级到 `V4.2.2 版本`；
	2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/黑屏升级 OMS 版本_2\|黑屏升级 OMS 版本_2]]，*多节点升级*，OMS 从 `V4.0.1 及之后`版本升级至 `V4.2.2`；
3. `OMS 运维`
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 全量数据迁移报错\|OMS 全量数据迁移报错]]，*升级 OMS 3.2.2 补丁包*；



### 参考文档







