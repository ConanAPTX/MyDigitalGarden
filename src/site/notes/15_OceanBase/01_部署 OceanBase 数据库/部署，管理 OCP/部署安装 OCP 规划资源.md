---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/部署安装 OCP 规划资源/","dgPassFrontmatter":true}
---


### 部署安装 OCP 规划资源
#### 1 OCP 服务器配置要求
##### 1.1 CPU 和内存
OCP-Server 机器的 CPU 和内存资源配置参考如下表所示。以下配置是以每个集群中包含 10 个租户为标准，进行计算得出的数据，请您根据实际情况进行计算，选择合适的 CPU 和内存；

| 管理的机器数量（台）   | 	CPU（核） |   内存（GB） |
| :------------- | :---------- | :------------ |
| ≤ 20 | 	8  | 	16 |
| ≤ 50  | 	16 	 | 32 |
| ≤ 200  | 	32  | 	64 |
| ≤ 400  | 	64  | 	128 |


> [!NOTE] 说明
> 1. OCP-Server 机器要求的最低配置为 8 核 16 GB 以及 500 GB 可用磁盘空间，当单个主机的租户数量 ≤ 20 时，仍建议 CPU 和内存保持为 8 核 16 GB；
> 2. 支持 400 台及以上的主机管理，未在真实环境验证，如果需要管理更多的主机，请与 OceanBase 的服务人员联系；


##### 1.2 磁盘容量
OCP-Server 在运行过程中会写入文件到磁盘，这些数据会占用磁盘空间，我们推荐 OCP-Server 至少需要预留 500 GB 的空间，以免因磁盘写满导致功能受影响；

`OCP 程序`：
存储路径：/home/admin/ocp-server/ 	
容量要求：OCP 程序安装后大约占用 1 GB 空间，自带 rpm 约占用 5 GB，建议预留 10 GB。

`OCP 主进程日志`：
存储路径：/home/admin/logs/ocp/ 	
容量要求：OCP 系统参数配置了 log 文件大小和保留的文件数量。 默认配置为单个文件 100 MB，每天至少 5-10 个文件，每个文件最多保留 100 天，建议预留 100 GB 空间。

`OCP 任务日志`：
存储路径：/home/admin/logs/task/ 	
容量要求：未设置容量限制，OCP 会定时清理任务日志，清理任务按照磁盘剩余空间大于 20 % 的目标清理日志文件。清理时，首先清理更新时间更早的日志文件，需预留 100 GB 空间。

`OCP 文件本地缓存`：
存储路径：/home/admin/data/files/ 	
容量要求：未设置容量限制，OCP 文件主要是 OCP 管理对象相关的安装包，且主要是 OceanBase 安装包，安装包大小平均在 500 MB 左右，按照 100 个安装包预估，需预留 100 GB 空间；


> [!NOTE] 说明：
> OCP-Server 的日志保留大小可以通过 OCP 系统参数进行配置，详见 [修改系统参数](https://www.oceanbase.com/docs/enterprise-oceanbase-ocp-cn-1000000000125948)，；


#### 2 MetaDB 资源
##### 2.1 CPU 和内存
MetaDB 用于存储除了监控数据之外的其它持久化数据，需要的资源相对较少。推荐分配不少于 4 个 CPU、8GB 内存；
推荐在 OceanBase 中创建独立的租户用于 MetaDB；

##### 2.2 磁盘容量
磁盘容量受 OCP 管控的集群规模、存储的文件资源和数据保留时间等因素的影响，一般推荐磁盘有 100 GB 以上。当发现磁盘资源不够时，请联系技术支持人员；

#### 3 MonitorDB 资源
详细情况请查看参考文档；

#### 4 主机资源
详细情况请查看参考文档；

### 4 参考文档
1. `V4.2.0 版本`：[OCP 配置服务器要求](https://www.oceanbase.com/docs/enterprise-oceanbase-ocp-cn-1000000000126433)，[MetaDB 资源](https://www.oceanbase.com/docs/enterprise-oceanbase-ocp-cn-1000000000126435)，[MonitorDB 资源](https://www.oceanbase.com/docs/enterprise-oceanbase-ocp-cn-1000000000126432)， [主机资源](https://www.oceanbase.com/docs/enterprise-oceanbase-ocp-cn-1000000000126434)，；



