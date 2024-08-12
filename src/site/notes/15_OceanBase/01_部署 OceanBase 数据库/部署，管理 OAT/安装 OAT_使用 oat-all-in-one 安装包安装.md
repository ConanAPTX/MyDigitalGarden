---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/安装 OAT_使用 oat-all-in-one 安装包安装/","dgPassFrontmatter":true}
---


### 安装 OAT_使用 oat-all-in-one 安装包安装
#### 1 前提条件
在部署 OAT 之前，您需要确认以下信息：

##### 1.1 您的操作系统满足以下条件：
| 服务器类型   | 操作系统 |   支持版本 |
| :------------- | :---------- | :------------ |
| x86_64 |   RHEL   | 7.2  |    及以上版本 |
| x86_64  |   	CentOS 	7.2  |    及以上版本  |   
| x86_64  |   	AliOS 	7.2  |    及以上版本  |   
| x86_64  |   	openSUSE   |   	12SP3 及以上 |   
| ARM aarch64  |   	AliOS   |   	7.2 及以上版本 |   
| ARM aarch64  |   	中标麒麟  |   	7.6 |   
| ARM aarch64  |   	华为 EulerOS  |   	2.0 SP8 |   

##### 1.2 您已经安装并启动 Docker。推荐 Docker 版本为社区版 17.03 及以上；

##### 1.3 OAT 的默认端口 `7000` 未被占用；
```shell
netstat -anp | grep 7000      # 查询端口是否被占用
```


#### 2 操作步骤，使用 oat-all-in-one 安装包安装

> [!NOTE] 说明：
> 推荐您使用 oat-all-in-one 安装包安装 OAT；

oat-all-in-one 安装包提供了 Docker 18.09.9，OAT 4.2.0，MetaDB 4.2.1、NLB 4.0.0 镜像及常用工具软件包；您可以从 [OceanBase 官网的下载中心](https://www.oceanbase.com/softwarecenter-enterprise) > 企业版 > OceanBase 管理者工具：获取 OAT 的 oat-all-in-one 安装包；

按照以下步骤安装 OAT：
```bash
# 1.解压 oat-all-in-one 安装包
tar -xf oat-all-in-one-x86.tar

# 2.运行安装脚本 `install.sh`
sh oat-all-in-one-x86/install.sh
	# 运行此命令将自动安装 Docker (如果您的环境没有 Dcoker)，部署 OAT 并扫描已有的镜像和工具软件包。 
	
# 3. 根据提示输入以下信息 
	docker root dir 和 OAT data dir：无需手动创建这两个目录；
	OAT HTTP listen port：默认 7000；
	OAT database port：默认 3306；
	OAT 初始密码等设置：；

# 4. 根据返回的 URL 访问 OAT，验证 OAT 服务的可用性。

```


> [!NOTE] `说明`：
> 1. `install.sh` 脚本启动 OAT 容器默认使用 `--net host`（主机网络模式）。如您需要配置为其他网络模式，请下载 OAT 容器手动启动；
> 2. x86（arrch64）版本的 `oat-all-in-one` 默认只包含 x86（aarch64）和 noarch 的镜像和软件包，如需 aarch64（x86）架构，请自行下载拷贝到 OAT 容器挂载的对应目录，然后发起扫描任务进行添加；



### 参考文档
1. `安装 OAT`：[V4.0.0](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-10000000001092535)，[V4.2.0](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-1000000000304881)，[V4.2.1](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-1000000000762607)，；


