---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/","dgPassFrontmatter":true}
---


### OceanBase 事务

#### 1 SQL 参考 > SQL 语法
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
		2. [[15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/OceanBase 数据库 Hint 清单\|OceanBase 数据库 Hint 清单]]，；
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

#### 2 分布式存储管理
1. *V3.2.3：分布式存储管理*
	1. [[15_OceanBase/02_OceanBase 基本操作/数据分布概述\|数据分布概述]]，数据分布概述
	2. 管理分区
	3. 管理副本
	4. 管理 Locality
	5. 负载均衡

#### 3 事务管理
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


#### 4 其他
[[15_OceanBase/03_OceanBase 高阶进阶/查询 clog 日志大小\|查询 clog 日志大小]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 查询超时\|OceanBase 查询超时]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 字符集与字符序\|OceanBase 字符集与字符序]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 悬挂事务与长事务\|OceanBase 悬挂事务与长事务]]，；
[[15_OceanBase/03_OceanBase 高阶进阶/死锁检测\|死锁检测]]，；


