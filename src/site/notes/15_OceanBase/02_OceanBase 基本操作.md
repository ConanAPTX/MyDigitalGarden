---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/","dgPassFrontmatter":true}
---



#### 1 数据库连接和路由
1. *V4.3.1：快速入手*
	1. *基于 MySQL 模式创建示例应用程序*
		1. 创建 Python 示例应用程序：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818548)，；
		2. 创建 Java 示例应用程序：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818550)，；
		3. 创建 C 示例应用程序：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818549)，；
		4. 创建 Go 示例应用程序：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818547)，；
	2. *基于 Oracle 模式创建示例应用程序*
		1. 创建 Java 示例应用程序：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818552)，；
		2. 创建 C 示例应用程序：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818551)，；
		3. [[15_OceanBase/02_OceanBase 基本操作/创建 Python 示例应用程序_Oracle 租户\|创建 Python 示例应用程序_Oracle 租户]]，当前不支持 Python 语言链接 Oracle 租户，但可以通过其他方式实现；
2. *V3.2.3：使用指南 > 数据库连接和路由 > 数据库连接和路由*
	1. 数据库连接和路由概
	2. 管理 OBProxy
	3. `连接数据库`
		1. *V4.3.1：应用开发 > 基于 MySQL 模式进行应用开发 > 连接 OceanBase 数据库*
			1. *连接方式概述*：[V4.3.1](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355644)，；
			2. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库#1 通过 MySQL 客户端连接数据库\|通过黑屏客户端连接 OB 数据库#1 通过 MySQL 客户端连接数据库]]，；
			3. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库#2 通过 OBClient 连接 OceanBase 租户\|通过黑屏客户端连接 OB 数据库#2 通过 OBClient 连接 OceanBase 租户]]，；
			4. *通过 ODC 连接数据库*：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355644)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818876)，；
		2. *V4.3.1：应用开发 > 基于 Oracle 模式进行应用开发 > 连接 OceanBase 数据库*
			1. *连接方式概述*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818830)，；
			2. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库#2 通过 OBClient 连接 OceanBase 租户\|通过黑屏客户端连接 OB 数据库#2 通过 OBClient 连接 OceanBase 租户]]，；
			3. *通过 ODC 连接数据库*：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355644)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818876)，；
			4. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/使用数据库连接池\|使用数据库连接池]]，；
	4. 物理连接
		1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/查询，终止租户会话\|查询，终止租户会话]]，*查询数据库连接情况*；包括物理连接；物理连接主要是指网络连接部分；
	5. 逻辑连接
		1. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/逻辑连接_ODP  会话\|逻辑连接_ODP  会话]]，包括：展示全部 Session，展示 Session 详细状态，终止 Server Session；查询 OBProxy 连接信息；
		1. 展示 Session 统计项
		2. 展示 Session 变量
	6. 驱动管理
	7. 路由管理


#### 2 集群和多租户管理
1. `集群管理`，*V3.2.3：使用指南 > 集群和多租户管理 > 管理集群*
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/查询集群信息\|查询集群信息]]，；
	3. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/集群租户管理概述\|集群租户管理概述]]，；
	4. *V4.3.1：集群管理 > 管理 Zone*
		1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/管理集群中的 Zone\|管理集群中的 Zone]]，；
	5. *V4.3.1：集群管理 > 管理 OBServer*
		1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/Server 管理及状态\|Server 管理及状态]]，；
2. OceanBase 配置管理
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 管理配置项，租户变量\|OceanBase 管理配置项，租户变量]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍\|OceanBase 配置项，变量详细介绍]]，；
3. 资源管理
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/OceanBase 资源管理概述\|OceanBase 资源管理概述]]，介绍`资源配置`，`资源单元`，`资源池`的概念；
	3. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/OceanBase 管理资源单元，资源池\|OceanBase 管理资源单元，资源池]]，介绍资源单元，资源池的管理，包括查询，创建，删除等；
	5. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/租户内资源的扩容和缩容\|15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/租户内资源的扩容和缩容]]，；
	6. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/查询 OB 资源分配情况\|查询 OB 资源分配情况]]，查询 OB 集群的 OBServer ，及租户的资源使用情况；
	7. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 集群资源池的分配，移除\|OceanBase 集群资源池的分配，移除]]
4. `租户管理`：*V3.2.3：使用指南 > 集群和多租户管理 > 管理租户*
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/OceanBase 管理租户_查询，创建，修改，删除租户\|OceanBase 管理租户_查询，创建，修改，删除租户]]，介绍查询，创建，修改，删除租户等；
	3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 网络安全访问控制\|OceanBase 网络安全访问控制]]，详细介绍查看，设置租户白名单；
	4. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/查看，修改租户的资源配置\|查看，修改租户的资源配置]]，；
	5. *OceanBase 租户会话管理*
		1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/查询，终止租户会话\|查询，终止租户会话]]，查询连接信息；
		2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/设置租户最大连接数\|设置租户最大连接数]]，介绍查询及设置租户的最大连接数；
	7. *管理用户和权限*

6. 管理数据存储
{ #c7aa88}

	1. *转储管理*，*合并管理*
		1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 转储管理概述\|OceanBase 转储管理概述]]，包括转储原理概述及转储管理；
		2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 合并管理概述\|OceanBase 合并管理概述]]，介绍 OB 数据库合并原理，合并方式，及如何修改合并方式；
		3. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/合并的触发方式\|合并的触发方式]]，介绍 OB 数据库合并的触发方式；
			1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 自适应合并\|OceanBase 自适应合并]]，；
			2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 手动触发，控制合并_V4.0.0 之前版本\|OceanBase 手动触发，控制合并_V4.0.0 之前版本]]，；
			3. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 手动触发，控制合并_V4.0.0 及以后版本\|OceanBase 手动触发，控制合并_V4.0.0 及以后版本]]，；
		4. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 数据库转储，合并参数设置\|OceanBase 数据库转储，合并参数设置]]，；
	2. *查询转储，合并的信息*
		1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/查询转储，合并的信息_V3.x 版本\|查询转储，合并的信息_V3.x 版本]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/查询转储，合并的信息_V4.x 版本\|查询转储，合并的信息_V4.x 版本]]，；
7. 管理内存
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存\|OceanBase 管理内存]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/查询内存的使用信息\|查询内存的使用信息]]，；
8. 管理进程
9. 管理日志
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase 日志概述\|OceanBase 日志概述]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase  日志级别，日志稳定性，日志控制方式\|OceanBase  日志级别，日志稳定性，日志控制方式]]，；
	3. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase  日志压缩与解压\|OceanBase  日志压缩与解压]]，；
	4. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/日志文件大小及数量管理\|日志文件大小及数量管理]]，；
	5. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/通用查找 SQL 请求日志\|通用查找 SQL 请求日志]]，；
	6. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/基于 Trace 功能查找上一次 SQL 请求日志\|基于 Trace 功能查找上一次 SQL 请求日志]]，；
	7. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase  Alert 日志\|OceanBase  Alert 日志]]，V4.2.3 版本开始新增 Alert 日志，用于展示告警相关日志信息；


#### 3 数据库对象管理
1. *参考指南 > SQL 参考 > 数据库对象管理*
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 数据库对象管理概述\|OceanBase 数据库对象管理概述]]，；

##### 3.1 数据库对象管理_MySql 模式
1. *V4.2.1：参考指南 > 数据库对象管理 > MySql 模式* ；
	1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理数据库_MySql 模式\|管理数据库_MySql 模式]]，；
	2. 管理表
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表_MySql 模式\|管理表_MySql 模式]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/查看指定租户中所有用户表\|查看指定租户中所有用户表]]，；
	3. *创建和管理分区*
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 分区表概述\|OceanBase 分区表概述]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/分区表_MySql 模式\|分区表_MySql 模式]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建分区表_MySql 模式\|创建分区表_MySql 模式]]，；
		4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 分区裁剪_MySql 模式\|OceanBase 分区裁剪_MySql 模式]]，；
		5. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/指定分区查询数据\|指定分区查询数据]]，适用于 Oracle 模式，MySql 模式；；
	4. 创建和管理表组
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表组_MySql 租户\|管理表组_MySql 租户]]，；
	5. *创建和管理索引*
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 索引概述\|OceanBase 索引概述]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 查看索引_MySql 模式\|OceanBase 查看索引_MySql 模式]]，；
		4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建，删除索引_MySql 租户\|OceanBase 创建，删除索引_MySql 租户]]，； 
			1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建空间索引_MySql 模式\|OceanBase 创建空间索引_MySql 模式]]，；
			2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 局部索引与全局索引的比较\|OceanBase 局部索引与全局索引的比较]]，；
			3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 创建局部索引，全局索引\|OceanBase 创建局部索引，全局索引]]，适用于 Oracle 模式，MySql 模式；
			4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建函数索引_MySql 模式\|OceanBase 创建函数索引_MySql 模式]]，； 
	6. 管理视图
	7. 管理序列
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建和管理序列_MySql 租户\|创建和管理序列_MySql 租户]]，；
	8. 管理触发器
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建和管理触发器_MySql 租户\|创建和管理触发器_MySql 租户]]，；

##### 3.2 数据库对象管理_Oracle 模式
1. *V4.2.1：参考指南 > 数据库对象管理 > Oracle 模式*；
	1. 管理表，表组，视图
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理表_Oracle 租户\|管理表_Oracle 租户]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/查询表的信息_Oracle 模式\|查询表的信息_Oracle 模式]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理表，表列，视图，视图列的注释\|管理表，表列，视图，视图列的注释]]，查询管理*表，表列，视图，视图列* 的注释；
		4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理视图_Oracle 模式\|管理视图_Oracle 模式]]，；
	2. *创建和管理分区*
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 分区表概述\|OceanBase 分区表概述]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/分区表_Oracle 模式\|分区表_Oracle 模式]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建分区表_Oracle 模式\|创建分区表_Oracle 模式]]，；
		4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/修改分区规则_Oracle 模式\|修改分区规则_Oracle 模式]]，；
		5. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/添加，删除，Truncate 分区_Oracle 模式\|添加，删除，Truncate 分区_Oracle 模式]]，；
		6. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 分区裁剪_Oracle 模式\|OceanBase 分区裁剪_Oracle 模式]]，；
		7. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/指定分区查询数据\|指定分区查询数据]]，适用于 Oracle 模式，MySql 模式；
	3. *创建和管理表组*
	4. *创建和管理索引*
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 索引概述\|OceanBase 索引概述]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建，删除索引_Oracle 租户\|创建，删除索引_Oracle 租户]]，Oracle 租户创建索引，包括 `唯一索引`，`局部索引`，`全局索引`，`函数索引`；
			1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 局部索引与全局索引的比较\|OceanBase 局部索引与全局索引的比较]]，；
			2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 创建局部索引，全局索引\|OceanBase 创建局部索引，全局索引]]，适用于 Oracle 模式，MySql 模式；
			3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 创建函数索引_Oracle 模式\|OceanBase 创建函数索引_Oracle 模式]]，；
		4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/查看索引_Oracle 租户\|查看索引_Oracle 租户]]，；
	5. 管理序列
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理序列_Oracle 租户\|管理序列_Oracle 租户]]，；
	6. 管理同义词
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理同义词_Oracle 租户\|管理同义词_Oracle 租户]]，；
	7. 管理触发器
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理触发器_Oracle 租户\|管理触发器_Oracle 租户]]，触发器概述和管理，包括：查询，禁用，启用，删除；
		2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建 DML 触发器_Oracle 租户\|创建 DML 触发器_Oracle 租户]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建 INSTEAD OF 触发器_Oracle 租户\|创建 INSTEAD OF 触发器_Oracle 租户]]，；
		4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建组合触发器_Oracle 租户\|创建组合触发器_Oracle 租户]]，；
	8. 管理目录
	9. 管理 DBLink
		1. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理 DBLink_Oracle 模式\|管理 DBLink_Oracle 模式]]，；

#### 4 SQL 参考 > SQL 语法
1. *V4.3.1：参考指南 > SQL 参考 > SQL 语法 > 系统租户*
2. *V4.3.1：参考指南 > SQL 参考 > SQL 语法 > 普通租户_MySql 模式*
	1. `基本元素 > 字面量`
		1. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/NULL 值\|NULL 值]]，包括 MySql，Oracle 模式；
	2. *SQL 语法 > 普通租户_MySql 模式 > 函数*
		1. [[15_OceanBase/02_OceanBase 基本操作/OB 系统函数_MySql 模式/函数概述，MySq 模式\|函数概述，MySq 模式]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/OB 系统函数_MySql 模式/OB 系统函数介绍_MySql 模式\|OB 系统函数介绍_MySql 模式]]，介绍 OceanBase 数据库 MySql 模式中的函数；
4. *V4.3.1：参考指南 > SQL 参考 > SQL 语法 > 普通租户_Oracle 模式*
	1. *SQL 语法 > 普通租户_Oracle 模式 > SQL 概述*
	2. *SQL 语法 > 普通租户_Oracle 模式 > 伪列*
		1. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/OceanBase 伪列概述\|OceanBase 伪列概述]]，，ROWNUM 函数，；
	3. *SQL 语法 > 普通租户_Oracle 模式 > 基本元素 *
		1. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/NULL 值\|NULL 值]]，包括 MySql，Oracle 模式；
		2. `注释`
			1. 注释概述
			2. SQL 语句的注释
			3. [Schema 与非 Schema 对象的注释](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000511610)，;
	4. *SQL 语法 > 普通租户_Oracle 模式 > 基本元素 > 注释 > Hint*
		1. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/Hint 概述\|Hint 概述]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/与访问路径相关的 Hint\|与访问路径相关的 Hint]]，与访问路径相关的 Hint 包括 `INDEX Hint` 和 `FULL Hint`；
		3. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 一致性级别的指定方式\|OceanBase 一致性级别的指定方式]]，；
	5. SQL 语法 > 普通租户_Oracle 模式 > 运算符 
	6. SQL 语法 > 普通租户_Oracle 模式 > 函数
		1. [[15_OceanBase/02_OceanBase 基本操作/OB 系统函数_Oracle 模式/函数概述，Oracle 模式\|函数概述，Oracle 模式]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/OB 系统函数_Oracle 模式/OB 系统函数介绍_Oracle 模式\|OB 系统函数介绍_Oracle 模式]]，介绍 OceanBase 数据库 Oracle 模式中的函数；
	7. SQL 语法 > 普通租户_Oracle 模式 > 表达式
	8. SQL 语法 > 普通租户_Oracle 模式 > 条件
		1. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/OceanBase 模式匹配条件_Oracle 模式\|OceanBase 模式匹配条件_Oracle 模式]]，；
		2. [[15_OceanBase/02_OceanBase 基本操作/管理数据库/副本介绍\|副本介绍]]，；
		3. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/EXISTS 条件\|EXISTS 条件]]，；
	9. SQL 语法 > 普通租户_Oracle 模式 > 查询和子查询
	10. SQL 语法 > 普通租户_Oracle 模式 > SQL 语句
	11. SQL 语法 > 普通租户_Oracle 模式 > DDL 功能
5. 参考指南 > SQL 参考 > SQL 实践和建议
6. 参考指南 > SQL 参考 > PL 参考

#### 5 分布式存储管理
1. *V3.2.3：分布式存储管理*
	1. [[15_OceanBase/02_OceanBase 基本操作/数据分布概述\|数据分布概述]]，数据分布概述
	2. 管理分区
	3. 管理副本
	4. 管理 Locality
	5. 负载均衡


#### 6 管理数据库
1. *V4.3.1：管理数据库*
	1. 登录 OceanBase 租户
	2. 集群管理
	3. 租户管理
	4. 副本管理
		1. [[15_OceanBase/02_OceanBase 基本操作/管理数据库/副本介绍\|副本介绍]]，副本介绍；
		2. 副本分布
		3. Locality 介绍
		4. Locality 常见操作
		5. 流量分布
		6. 数据分布
		7. 数据负载均衡
			1. [[15_OceanBase/02_OceanBase 基本操作/管理数据库/数据负载均衡概述\|数据负载均衡概述]]，数据负载均衡概述；
			2. Transfer Partition
			3. 取消 Transfer Partition
			4. 取消 Balance Job
	5. 高可用
	6. 安全权限
	7. 备份恢复
	8. 监控指标
	9. 日志
	10. 日常巡检
	11. 性能调优
	12. 问题排查
	13. 应急处理


