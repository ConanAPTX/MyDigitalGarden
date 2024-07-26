---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/","dgPassFrontmatter":true}
---


### OceanBase 事务

#### 1 事务管理
1. *V3.2.3：系统架构 > 事务管理 > 事务*
	1. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 事务简介\|OceanBase 事务简介]]，事务简介；
	2. 事务的结构
	3. 语句级原子性
	4. 全局时间戳
	5. 事务控制
		1. 事务控制概述
		2. 活跃事务
		3. Savepoint
		4. 事务控制语句
			1. MySQL 事务控制
			2. Oracle 事务控制
				1. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 事务控制\|OceanBase 事务控制]]，；
	6. Redo 日志
	7. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 事务分类\|OceanBase 事务分类]]，包括：本地事务，分布式事务，两阶段提交；
	8. XA 事务
3. *V3.2.3：系统架构 > 事务管理 > 事务并发和一致性*
	1. 数据并发性和一致性概述
	2. 多版本读一致性
		1. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 全局时间戳\|OceanBase 全局时间戳]]，；
	3. 并发控制
		1. 并发控制概述
		2. 锁机制
	4. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 事务隔离级别\|OceanBase 事务隔离级别]]，包括 Oracle，MySql 模式；
	5. `OceanBase 弱一致性读`
		1. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 弱一致性读\|OceanBase 弱一致性读]]，；
		2. [[15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 一致性级别的指定方式\|OceanBase 一致性级别的指定方式]]，；


#### 2 其他
[[15_OceanBase/03_OceanBase 高阶进阶/查询 clog 日志大小\|查询 clog 日志大小]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 查询超时\|OceanBase 查询超时]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 字符集与字符序\|OceanBase 字符集与字符序]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 悬挂事务与长事务\|OceanBase 悬挂事务与长事务]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/死锁检测\|死锁检测]]，；


