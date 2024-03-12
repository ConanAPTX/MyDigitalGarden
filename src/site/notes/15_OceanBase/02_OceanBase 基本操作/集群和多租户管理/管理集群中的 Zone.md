---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理集群中的 Zone/","dgPassFrontmatter":true}
---



### 管理集群中的 Zone
#### 1 Zone 管理概述  
一个 OceanBase 集群由若干个 Zone 组成。Zone 是可用区（Availability Zone）的简写。Zone 本身是一个逻辑概念，是对物理机进行管理的容器，一般是同一机房的一组机器的组合。从物理层面来讲，一个 Zone 通常等价于一个机房、一个数据中心或一个 IDC。为交付高级别的数据安全性和服务可用性能力，一个 OceanBase 集群通常会分布在同城的 3 个机房中，同一份数据的三个副本分别分布在 3 个机房中（即 3 个 Zone 中）；  

OceanBase 数据库支持数据跨地域（Region）部署，且不同 Region 的距离通常较远，从而满足地域级容灾的需求。一个 Region 可以包含一个或者多个 Zone。例如，某 OceanBase 集群部署在某城市（某一 Region）且分布在三个 IDC 机房中，每个 IDC 机房中又有 3 台服务器，所以每个 IDC 机房的中的这 3 台服务器组合成一个 Zone；  

> 在 OceanBase 数据库中，Zone 主要有以下两种类型：  
  1. 读写 Zone 具备读写属性的 Zone，支持部署全功能型副本、只读型副本、普通日志型副本；  
  2. 加密 Zone 具备加密属性的 Zone，仅支持部署加密日志型副本，不支持部署全功能型副本、只读型副本、普通日志型副本；  

#### 2 增加或删除 Zone  
可以通过 SQL 语句或 OCP 来增加或删除 Zone；  
在集群中增加或删除 Zone 的操作通常用于集群扩容或缩容等需求场景；  

##### 2.1 通过 SQL 语句增加或删除 Zone  
详细语法请参考：[增加或删除 Zone](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946230)，；  

```sql  
-- 在集群中新增一个名为 zone1的 Zone  
obclient> ALTER SYSTEM ADD ZONE zone1;  
  
-- 在集群中删除名为 zone1 的 Zone  
obclient> ALTER SYSTEM DELETE ZONE zone1;  
```  

##### 2.2 通过 OCP 增加 Zone，删除 Zone  

#### 3 增加或删除加密 Zone  
OceanBase 数据库提供了 Zone 级别的日志传输和保存加密功能，您可以在集群中添加 Zone 时，指定 Zone 的加密属性，后续在向该加密 Zone 发送日志时，系统会对 Clog 进行加密，并且该 Zone 在持久化 Clog 时也会加密；  
详细情况请参考：[增加或删除加密 Zone](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946231)，； 

#### 4 启动或停止 Zone  
可以通过 SQL 语句或 OCP 来启动或停止 Zone；  
在集群中启动或停止 Zone 的操作通常用于允许或禁止 Zone 内的所有物理服务器对外提供服务的需求场景；  

##### 4.1 通过 SQL 语句启动和停止 Zone  
```sql  
obclient> ALTER SYSTEM START ZONE zone1; -- 在集群中启动名为 zone1的 Zone  
obclient> ALTER SYSTEM STOP ZONE zone1; -- 在集群中停止运行名为 zone1的 Zone  
obclient> ALTER SYSTEM FORCE STOP ZONE Zone1; --在集群中强制停止运行名为 zone1的 Zone  
```  
详细语法请参考：[启动或停止 Zone](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946232)，；  

##### 4.2 通过 OCP 停止 Zone  

#### 5 修改 Zone 的配置信息  
[修改 Zone 的配置信息](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946233)，；  

#### 6 重启 Zone  
OCP 提供了白屏化重启 Zone 的功能；  
说明：OceanBase 数据库暂时没有单独的命令行重启 zone 操作；  
详细情况：[重启 Zone](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946234)；  

#### 7 隔离故障 Zone  
当 Zone 发生故障时，您可以隔离故障 Zone。隔离后，新的读写请求不会路由到该故障 Zone 内的进程上。执行隔离故障 Zone 后，该 Zone 处于 stopped 状态。隔离操作会限制本 Zone 上面不能有主副本(Leader)，将故障 Zone 上的 Leader 切换到其他机器上，以此尝试恢复用户的写服务和集群内的日志同步；  
  
```sql  
-- 隔离故障的机器(使用 root 用户登录数据库的 sys 租户)  
obclient> ALTER SYSTEM ISOLATE ZONE 'zone1';  

-- 隔离故障的 Zone 后，解除隔离状态命令(使用 root 用户登录数据库的 sys 租户)  
ALTER SYSTEM START ZONE 'zone1'  
```  


### 参考文档



