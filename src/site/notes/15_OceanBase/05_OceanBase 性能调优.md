---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/","dgPassFrontmatter":true}
---


### OceanBase 性能调优
 _V4.2.1:参考指南 > 性能调优 _

#### 1 性能相关工具
1. _V4.2.1:参考指南 > 性能调优 > 性能相关工具_
2. 系统工具
	1. Top 通用工具
	2. CPU 工具
	3. 内存工具
	4. 磁盘 I/0 工具
	5. 网络工具
3. 内部表
	1. gv$sysstat
	2. `gv$sql_audit`，`gv$ob_sql_audit`
		1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/gv, sql_audit 视图\|gv, sql_audit 视图]]，介绍 SQL AUDIT 的开启及清理；
		2. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 gv$sql_audit 视图_2.x，3.x\|查询 gv$sql_audit 视图_2.x，3.x]]，；
		3. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 gv$ob_sql_audit 视图_4.x\|查询 gv$ob_sql_audit 视图_4.x]]，；
	3. `__all_virtual_trans_stat`
4. 日志
	1. slow trans
	2. tenant dump
	3. slow query
5. OCP，；


#### 2 系统调优
1. _V4.2.1:参考指南 > 性能调优 > 系统调优_
2. `OceanBase 系统调优`
	1. [[15_OceanBase/05_OceanBase 性能调优/租户资源管理与调优\|租户资源管理与调优]]，；
	2. [[15_OceanBase/05_OceanBase 性能调优/转储合并的监控与调优\|转储合并的监控与调优]]，；
	3. [[15_OceanBase/05_OceanBase 性能调优/Clog 性能调优\|Clog 性能调优]]，；
	4. [[15_OceanBase/05_OceanBase 性能调优/OceanBase 线程管理\|OceanBase 线程管理]]，包括*租户线程管理*，*系统后台线程管理*；
3. 操作系统参数调优
4. OceanBase 数据库参数调优
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 管理配置项，租户变量\|OceanBase 管理配置项，租户变量]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍\|OceanBase 配置项，变量详细介绍]]，；
5. OBProxy 参数调优
{ #9c71b0}

	1. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数设置，及调优\|OBProxy 配置参数设置，及调优]]，；
		1. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 生产环境建议使用参数\|OBProxy 生产环境建议使用参数]]，详细介绍OBProxy 生产环境建议使用的参数；
	2. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数详细介绍\|OBProxy 配置参数详细介绍]]，；


#### 3 性能诊断
1. _V4.2.1:参考指南 > 性能调优 > 性能诊断_
2. 集群性能诊断
3. 租户性能诊断
4. OBProxy 性能监控
5. 主机性能监控


#### 4 业务模型调优
1. _V4.2.1:参考指南 > 性能调优 > 业务模型调优_
2. 负载均衡
3. Sql 诊断
4. 分布式事务


#### 5 Sql 调优指南

1.  _V4.3.2:管理数据库> 性能调优 > SQL 调优_
	1. SQL 优化体系
		1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询 TOP SQL\|查询 TOP SQL]]，；
	2. 执行计划优化
		1. 统计信息和估行机制
			1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/统计信息\|统计信息]]，；
			2. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查询统计信息\|查询统计信息]]，；
			3. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/管理统计信息\|管理统计信息]]，；
			4. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/优化器估行机制\|优化器估行机制]]，；
		2. 【计划管理】，【管理执行计划】：_V4.2.1:参考指南 > 性能调优 > Sql 调优指南 > SQL 优化 > 管理执行计划_：
			1.  [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/计划绑定\|计划绑定]]，；
			2. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/OceanBase 执行计划管理\|OceanBase 执行计划管理]]，；
		3. 索引选择：[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 索引概述\|OceanBase 索引概述]]，；
		4. 连接：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/连接简介\|连接简介]]，；
			1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/连接次序\|连接次序]]，；
		5. 排序和 limit 优化：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/排序和 limit 优化\|排序和 limit 优化]]，；
	3. 业务逻辑优化
		1. SQL 规范：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/SQL 规范概述\|SQL 规范概述]]，；
		2. Schema 规范
	4. [[15_OceanBase/OceanBase SQL 优化实践示例\|OceanBase SQL 优化实践示例]]，；
	5. SQL 调优典型场景和案例

##### 5.1 

1. [[15_OceanBase/05_OceanBase 性能调优/SQL 调优方法\|SQL 调优方法]]，；
2. _V4.2.1:参考指南 > 性能调优 > Sql 调优指南_：[Sql 调优指南](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218532)，；
3. SQL 请求执行流程，[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/Sql 请求执行流程\|Sql 请求执行流程]]，；
	1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/DML，DDL 语句处理\|DML，DDL 语句处理]]，；
4. SQL 执行计划
	1. SQL 执行计划简介
		1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/EXPLAIN，查看执行计划\|EXPLAIN，查看执行计划]]，；
	2. [[15_OceanBase/05_OceanBase 性能调优/执行计划算子\|执行计划算子]]，详细介绍 OceanBase 执行计划算子；
	3. 执行计划缓存：[[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/执行计划缓存\|执行计划缓存]]，；
		1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/计划缓存相关的视图\|计划缓存相关的视图]]，；
		2. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/查看 SQL 执行的物理执行计划\|查看 SQL 执行的物理执行计划]]，查询 sql 实际执行计划 ；
	5. 快速参数化
	6. 实时执行计划展示
	7. SQL Explain 优化实践
	8. 
	9. 执行计划优化：_V4.3.2:管理数据库> 性能调优 > SQL 调优 > 执行计划优化_

5. 分布式执行计划
	1. 分布式执行和并行查询
	2. 分布式计划的生成
	3. 分布式执行计划调度
	4. 分布式执行计划管理
	5. 并行开启方式及优先级
	6. Auto DOP
	7. 并行查询的执行
	8. 并行查询的参数调优
	11. 并行 DML
6. 并行执行计划
	1. 并行执行简介
	2. 并行执行分类与优化
	3. 设置并发执行
	4. 并行执行问题诊断
	5. 并行执行调优技巧
	6. 并行执行入门实践
7. _V4.2.1:参考指南 > 性能调优 > Sql 调优指南 > SQL 优化_：[SQL 优化](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218944)，；
	1. SQL 调优概览
	2. SQL 调优基本流程
	3. SQL 执行性能监控
		1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/gv, sql_audit 视图\|gv, sql_audit 视图]]，可以查看每一次 SQL 请求的来源、执行状态等统计信息；
		2. [[15_OceanBase/05_OceanBase 性能调优/捞取慢 SQL\|捞取慢 SQL]]，在 OBServer 日志中查找慢 SQL；
		3. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/SQL Trace\|SQL Trace]]，通过 SQL Trace 查看执行过程信息及各阶段的耗时；
		4. 计划缓存视图
		5. SQL 性能分析示例：该部分内容迁移至：[[15_OceanBase/OceanBase SQL 优化实践示例\|OceanBase SQL 优化实践示例]]，；
	4. 优化器统计信息
	5. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/OceanBase 查询改写\|OceanBase 查询改写]]，；
	6. 查询优化
8. 相关术语
9. SQL 调优常见问题






### 7 性能测试
1. _V4.2.1:参考指南 > 性能测试_
1. MySql 模式
	1. Sybench OLTP
	2. BenchMarkSQL 
	3. TPC_H
2. Oracle 模式性能测试
	1. BenchMarkSQL
	2. TPC-H

#### 1 案例分析
1. [[15_OceanBase/05_OceanBase 性能调优/大查询线程的管理及调度机制\|大查询线程的管理及调度机制]]，；





1. _V4.3.2:管理数据库> 性能调优_
	1. 性能调优概述
	2. 识别链路上的瓶颈
	3. 识别组件内的瓶颈
		1. [[15_OceanBase/ODP 性能瓶颈\|ODP 性能瓶颈]]，ODP 性能瓶颈；
			1. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 慢查询日志\|OBProxy 慢查询日志]]，介绍获取 obproxy 慢查询日志，并解析；
		3. ODP 性能分析
		4. OBServer 端性能瓶颈
	4. SQL 调优：该部分已迁移走；
