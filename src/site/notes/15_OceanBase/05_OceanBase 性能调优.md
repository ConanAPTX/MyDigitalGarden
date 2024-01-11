---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/","dgPassFrontmatter":true}
---


### OceanBase 性能调优
#### 1 性能相关工具
1. 系统工具
2. 内部表
	1. [[15_OceanBase/05_OceanBase 性能调优/gv，sql_audit 视图\|gv，sql_audit 视图]]，；
3. 日志
4. OCP，；

#### 2 系统调优
1. 操作系统参数调优
2. 数据库参数调优
3. OBProxy 参数调优
{ #9c71b0}

	1. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数设置，及调优\|OBProxy 配置参数设置，及调优]]，；
	2. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数详细介绍\|OBProxy 配置参数详细介绍]]，；



[[15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍\|OceanBase 配置项，变量详细介绍]]，；

#### 3 业务模型调优
1. 负载均衡
2. Sql 诊断
3. 分布式事务

#### 4 Sql 调优指南
1. [[15_OceanBase/05_OceanBase 性能调优/Sql 请求执行流程\|Sql 请求执行流程]]，；
2. Sql 执行计划
	1. [[15_OceanBase/05_OceanBase 性能调优/EXPLAIN，查看执行计划\|EXPLAIN，查看执行计划]]，；
	2. [[如何设置和查看 SQL 执行计划 \|如何设置和查看 SQL 执行计划 ]]，查询 sql 实际执行计划 ；
	3. 
3. 分布式执行计划
4. Sql 优化
5. Sql 调优常见问题

#### 5 性能诊断
1. 集群性能诊断
2. 租户性能诊断
3. OBProxy 性能监控
4. 主机性能监控


#### 6 性能测试
1. MySql 模式
	1. Sybench OLTP
	2. BenchMarkSQL 
	3. TPC_H
2. Oracle 模式性能测试
	1. BenchMarkSQL
	2. TPC-H

#### 7 案例分析
1. [[15_OceanBase/05_OceanBase 性能调优/大查询线程的管理及调度机制\|大查询线程的管理及调度机制]]，；