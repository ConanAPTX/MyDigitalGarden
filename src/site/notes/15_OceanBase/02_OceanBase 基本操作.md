---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/","dgPassFrontmatter":true}
---


#### 1 数据库连接和路由
1. 数据库连接和路由概
3. 连接数据库
	1. [[15_OceanBase/02_OceanBase 基本操作/连接 OceanBase 数据库\|连接 OceanBase 数据库]]，；
4. 物理连接
5. 逻辑连接
6. 驱动管理
7. 路由管理


#### 2 集群和多租户管理
1. 集群管理
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/查询集群信息\|查询集群信息]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理集群中的 Zone\|管理集群中的 Zone]]，；
	3. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/集群租户管理概述\|集群租户管理概述]]，；
2. OceanBase 配置管理
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OB 配置管理/OceanBase 管理配置项，租户变量\|OceanBase 管理配置项，租户变量]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍\|OceanBase 配置项，变量详细介绍]]，；
3. 资源管理
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理资源/OB 资源管理概述\|OB 资源管理概述]]，介绍`资源配置`，`资源单元`，`资源池`的概念；
	3. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理资源/OB 管理资源单元，资源池\|OB 管理资源单元，资源池]]，；
	5. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理资源/租户内资源的扩容和缩容\|租户内资源的扩容和缩容]]，；
	6. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理资源/查询 OB 资源分配情况\|查询 OB 资源分配情况]]，查询 OB 集群的 OBServer ，及租户的资源使用情况；
	7. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OceanBase 集群资源池的分配，移除\|OceanBase 集群资源池的分配，移除]]
4. 租户管理
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/管理租户_查询，创建，修改，删除租户\|管理租户_查询，创建，修改，删除租户]]，；
	4. [[15_OceanBase/04_OceanBase 高可用和容灾/OceanBase 网络安全访问控制\|OceanBase 网络安全访问控制]]，详细介绍查看，设置租户白名单；
	5. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/查看，修改租户的资源配置\|查看，修改租户的资源配置]]，；
	6. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/OceanBase 租户会话管理\|OceanBase 租户会话管理]]，；
	7. `管理用户和权限`
		1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/管理用户和权限/查询用户权限_MySql 模式\|查询用户权限_MySql 模式]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/管理用户和权限/查询用户权限_Oracle 模式\|查询用户权限_Oracle 模式]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/管理用户和权限/修改用户密码\|修改用户密码]]，； 
5. 管理数据存储
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/合并管理\|合并管理]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理数据存储/OceanBase 转储管理概述\|OceanBase 转储管理概述]]，；
	3. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理数据存储/OceanBase 合并管理概述\|OceanBase 合并管理概述]]，；
	4. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理数据存储/查询转储，合并的信息\|查询转储，合并的信息]]，；
6. 管理内存
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OceanBase 管理内存\|OceanBase 管理内存]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/查询内存的使用信息\|查询内存的使用信息]]，；
7. 管理进程
8. 管理日志
	1. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/日志打印级别\|日志打印级别]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/日志文件大小及数量管理\|日志文件大小及数量管理]]，；
	3. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/通用查找 SQL 请求日志\|通用查找 SQL 请求日志]]，；
	4. [[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/基于 Trace 功能查找上一次 SQL 请求日志\|基于 Trace 功能查找上一次 SQL 请求日志]]，；


#### 3 数据库对象管理
##### 3.1 Oracle 模式
1. 管理表
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理表_Oracle 租户\|管理表_Oracle 租户]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理索引_Oracle 租户\|管理索引_Oracle 租户]]，；
2. 管理表组
3. 管理视图
4. 管理序列
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理序列_Oracle 租户\|管理序列_Oracle 租户]]，；
5. 管理同义词
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理同义词_Oracle 租户\|管理同义词_Oracle 租户]]，；
6. 管理触发器
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理触发器_Oracle 租户\|管理触发器_Oracle 租户]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建 DML 触发器_Oracle 租户\|创建 DML 触发器_Oracle 租户]]，；
7. 管理目录
8. 管理 DBLink
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理 DBLink_Oracle 模式\|管理 DBLink_Oracle 模式]]，；

##### 3.2 MySql 模式
1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理数据库_MySql 模式\|管理数据库_MySql 模式]]，；
2. 管理表
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表_MySql 模式\|管理表_MySql 模式]]，；
3. 管理表组
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表组_MySql 租户\|管理表组_MySql 租户]]，；
4. 管理索引
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理索引_MySql 租户\|管理索引_MySql 租户]]，；
5. 管理视图
6. 管理序列
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建和管理序列_MySql 租户\|创建和管理序列_MySql 租户]]，；
7. 管理触发器
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建和管理触发器_MySql 租户\|创建和管理触发器_MySql 租户]]，；


#### 4 分布式存储管理
1. 数据分布概述
2. 管理分区
3. 管理副本
4. 管理 Locality
5. 负载均衡

#### 5 Oracle 租户 SQL 语法
1. [[15_OceanBase/02_OceanBase 基本操作/OceanBase 模式匹配条件\|OceanBase 模式匹配条件]]，；

