---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/","dgPassFrontmatter":true}
---


### 部署 OceanBase 数据库
#### 1 部署，管理 OAT，OceanBase 管理者工具
1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/安装 OAT_使用 OAT 安装包安装\|安装 OAT_使用 OAT 安装包安装]]，使用 OAT 安装包安装；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/安装 OAT_使用 oat-all-in-one 安装包安装\|安装 OAT_使用 oat-all-in-one 安装包安装]]，【推荐使用】；
3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/管理者工具 OAT 升级指南_V3.2 升级至 V4.1\|管理者工具 OAT 升级指南_V3.2 升级至 V4.1]]，；

#### 2 部署，管理 OCP，OceanBase 开发者中心
1. 在 OAT 中安装单节点 OCP
	1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署安装 OCP 规划资源\|部署安装 OCP 规划资源]]，需要规划的资源包括：OCP 配置服务器要求、MetaDB 资源、MonitorDB 资源 和 主机资源；
	2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/准备部署环境\|准备部署环境]]，；
	3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署单点 OCP\|部署单点 OCP]]，；
	4. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署 OCP 后的检查\|部署 OCP 后的检查]]，；
2. 在 OAT 中安装高可用模式 OCP
3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/获取 OCP 相关账号密码\|获取 OCP 相关账号密码]]，；


#### 3 部署，管理 OceanBase 数据库
##### 3.1 部署，管理 OceanBase 数据库
1. [快速上手](https://open.oceanbase.com/quickStart)，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/启动 observer 进程\|启动 observer 进程]]，；

##### 3.2 部署 管理 OBProxy
1. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/OBProxy 部署\|OBProxy 部署]]，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/部署 OBProxy_命令行\|部署 OBProxy_命令行]]，；
3. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/生产环境运行 OBProxy - 守护进程\|生产环境运行 OBProxy - 守护进程]]，；
4. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/OBProxy 管理概述\|OBProxy 管理概述]]，；
5. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/管理 OBProxy 集群\|管理 OBProxy 集群]]，；
6. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/管理 OBProxy_启动，停止 OBProxy\|管理 OBProxy_启动，停止 OBProxy]]，；
7. `OBProxy 参数调优`，详细情况请参考：[[15_OceanBase/05_OceanBase 性能调优#^9c71b0\|05_OceanBase 性能调优#^9c71b0]]，；
8. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/如何查看当前 obproxy 集群的启动模式\|如何查看当前 obproxy 集群的启动模式]]，；
9. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/分析 observer 日志\|分析 observer 日志]]，；
10. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/拷贝 Oceanbase 日志文件\|拷贝 Oceanbase 日志文件]]，；


##### 3.3 部署，管理 ODC
1. `下载 ODC，OceanBase 开发者中心`
	1. [下载桌面版 ODC](https://help.aliyun.com/document_detail/212816.html)，可以下载各版本的桌面版 ODC 安装包；除此之外还可以在：[OceanBase 软件下载中心](https://www.oceanbase.com/softwarecenter-enterprise)，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/获取 ODC 日志\|获取 ODC 日志]]，；

#### 4 OMS
1. [[15_OceanBase/01_部署 OceanBase 数据库/OMS 版本升级\|OMS 版本升级]]，；
2. [[15_OceanBase/90_/OMS 全量数据迁移报错\|OMS 全量数据迁移报错]]，升级 OMS 3.2.2 补丁包；

#### 5 OBLOADER
1. [[15_OceanBase/01_部署 OceanBase 数据库/安装 mydumper，myloader 工具\|安装 mydumper，myloader 工具]]，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/安装导数工具 OBLOADER\|安装导数工具 OBLOADER]]，；



### 参考文档







