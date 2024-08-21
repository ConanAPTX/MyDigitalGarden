---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/","dgPassFrontmatter":true}
---


### OceanBase 常用 Sql 语句
#### 1 连接 OB 数据库
1. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库\|通过黑屏客户端连接 OB 数据库]]，；
2. *OceanBase 租户会话管理*
	1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/查询，终止租户会话\|查询，终止租户会话]]，查询连接信息；*查询数据库连接情况*；包括物理连接；物理连接主要是指网络连接部分；
	2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/设置租户最大连接数\|设置租户最大连接数]]，介绍查询及设置租户的最大连接数；
3. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/逻辑连接_ODP  会话\|逻辑连接_ODP  会话]]，包括：展示全部 Session，展示 Session 详细状态，终止 Server Session；查询 OBProxy 连接信息；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/Sql 相关 timeout 设置\|Sql 相关 timeout 设置]]，；

#### 2 OceanBase 日常巡检，运维
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/日常巡检/检查主机信息\|检查主机信息]]，；
2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/Server 管理及状态#1 检查 OBServer 状态\|Server 管理及状态#1 检查 OBServer 状态]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/日常巡检/检查 NTP 偏移量\|检查 NTP 偏移量]]，；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase SQL 优化常用语句\|OceanBase SQL 优化常用语句]]，；

##### 2.1 运维最佳实践
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/运维最佳实践/OceanBase 集群级别的扩容和缩容\|OceanBase 集群级别的扩容和缩容]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/运维最佳实践/OceanBase 租户内资源的扩容和缩容\|OceanBase 租户内资源的扩容和缩容]]，；


#### 3 OceanBase 运维，监控与异常处理
1. 用户权限管理
2. 日志查询
3. 日常运维操作
4. 数据库监控
5. 常见异常处理
	1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/运维，监控与异常处理/Ocean Base 错误信息概述\|Ocean Base 错误信息概述]]，；
	2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/运维，监控与异常处理/问题排查概述：副本迁移问题排查\|问题排查概述：副本迁移问题排查]]，；
6. 灾难恢复

#### 4 OceanBase 常用查询语句
1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/查询 OceanBase 集群，Zone 信息\|查询 OceanBase 集群，Zone 信息]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查看 OBServer 状态\|查看 OBServer 状态]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查询 OceanBase 租户信息\|查询 OceanBase 租户信息]]，；
4. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/查询 OceanBase 资源分配情况\|查询 OceanBase 资源分配情况]]，查询 OB 集群的 OBServer ，及租户的资源使用情况；
5. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查询磁盘使用情况_v2.x，v3.x\|查询磁盘使用情况_v2.x，v3.x]]，；
6. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查询磁盘使用情况_V4.x\|查询磁盘使用情况_V4.x]]，；
7. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/在 oceanbase 中查询数据库信息\|在 oceanbase 中查询数据库信息]]，；
8. *查询 OceanBase 表的信息*
	1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/Oceanase 查询数据库对象情况\|Oceanase 查询数据库对象情况]]，；
	2. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/查看指定租户中所有用户表\|查看指定租户中所有用户表]]，；
	3. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/查询表的信息_Oracle 模式\|查询表的信息_Oracle 模式]]，；
	4. [[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理表，表列，视图，视图列的注释\|管理表，表列，视图，视图列的注释]]，查询管理*表，表列，视图，视图列* 的注释；
9. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/123124\|123124]]，；
10. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OceanBase  集群副本分布情况\|查询 OceanBase  集群副本分布情况]]，；
11. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查询服务版本\|查询服务版本]]，；
12. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/OceanBase  其他常用查询\|OceanBase  其他常用查询]]，；
13. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/申请工单需要提供的数据\|申请工单需要提供的数据]]，；


#### 5 获取，拷贝，分析 OB 相关日志
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/分析 observer 日志\|分析 observer 日志]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_1\|拷贝 Oceanbase 日志文件_1]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_2\|拷贝 Oceanbase 日志文件_2]]，ZJGY 拷贝 OBServer 日志文件到本地；；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 OMS 日志\|获取 OMS 日志]]，；
5. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 ODC 日志\|获取 ODC 日志]]，；
6. [[15_OceanBase/20_OceanBase 常用 Sql 语句/tcpdump  命令\|tcpdump  命令]]，；


#### 6 其他
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/sql_plan_monitor\|15_OceanBase/20_OceanBase 常用 Sql 语句/sql_plan_monitor]]，；
2.  [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/1231312312\|1231312312]]，；

#### 7 
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 系统视图概述_V4.x\|OceanBase 系统视图概述_V4.x]]，；
2. [[15_OceanBase/99_OceanBase 内部表介绍\|99_OceanBase 内部表介绍]]，；

### 参考文档




```sql
-- log盘满：查询表__all_virtual_server_clog_stat，清除较老的日志
SELECT svr_min_log_timestamp FROM oceanbase.__all_virtual_server_clog_stat WHERE zone_status='ACTIVE';
```



