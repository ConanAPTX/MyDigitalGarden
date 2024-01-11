---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/OBProxy 管理概述/","dgPassFrontmatter":true}
---


### OBProxy 管理概述

OceanBase 数据库是分布式数据库，每个表甚至每个表的不同分区都可能存放在不同的机器上。想要对表进行读写，必须先要定位到数据所属的表或是分区的主副本位置，然后才能执行相应的SQL DML 语句，这在应用层面而言是几乎不可能做到的；

OceanBase Database Proxy（简称 ODP）是 OceanBase 数据库专用的代理服务器。
OceanBase 数据库的用户数据以多副本的形式存放在各个 OBServer 上，ODP 接收用户发出的 SQL 请求，并将 SQL 请求转发至最佳目标 OBServer，最后将执行结果返回给用户；


#### 1 OBProxy 概述
OBProxy 作为OceanBase 数据库专用的反向代理软件，其核心功能就是路由以将客户端发起的数据访问请求转发到正确的 OBServer 上；

客户端通过 OBProxy 访问 OceanBase 数据库的数据链路如图所示，用户通过任意Client 驱动发出请求，请求通过负载均衡组件访问到任意一台无状态的 OBProxy 上，然后 OBProxy 再将用户请求转发到后端 OceanBase 集群中最佳的 OBServer 上去执行；

#### 2 OBProxy核心功能: 
##### 2.1 路由:
OBProxy 作为 OceanBase的高性能反向代理服务器，其核心功能就是路由转发 OBProxy 路由的目标是将具体 SQL 转发到最恰当的 Server 上执行，路由的核心过程包括：简单的 SQL Parser，LDC 路由，读写分离，备优先读，黑名单机制；

##### 2.2 连接管理:
 在 observer 宕机/升级/重启时，客户端与 OBProxy 的连接不会断开，OBProxy 可以迅速切换到正常的 server上，对应用透明；
  OBProxy 支持用户通过同一个 OBProxy 访问多个 OceanBase 集群；
 Server session 对于每个 client session 独占；
 同一个 client session 对应 server session 状态保持相同(session变量同步)；



#### 3 OBProxy 执行流程 
>OBProxy 收到用户请求后，OBProxy 的执行流程：
>1. parse sql，通过简易parse，解析出sql涉及的table name、database name
>2. fetch route entry，根据用户的tenant name、database name、table name、partition id 向observer拉取该partition的路由表;
>3. sort route entry，根据各种相关属性对路由表中ip进行排序
>	1. read_consisitency：强一致性读or弱一致性读
>	2. 目标server状态：正在合并or常态
>	3. 路由精准度： PS or TS
>	4. LDC 匹配： 本地、同城、异地
>	5. Zone 类型
>	6. 读写分离的ob_route_policy取值
>4. filter by congestion，从路由表中依次尝试目标ip，通过黑名单进行过滤
>5. forward request，将用户请求转发给目标server
 
 


### 参考文档：