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
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/日常巡检/检查 NTP 偏移量\|检查 NTP 偏移量]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/申请工单需要提供的数据\|申请工单需要提供的数据]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase SQL 优化常用语句\|OceanBase SQL 优化常用语句]]，；
4. OceanBase 运维
	1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 集群级别的扩容和缩容\|OceanBase 集群级别的扩容和缩容]]，；
	2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 租户内资源的扩容和缩容\|OceanBase 租户内资源的扩容和缩容]]，；


#### 3 OceanBase 运维，监控与异常处理
1. 用户权限管理
2. 
3. 日志查询
4. 日常运维操作
5. 数据库监控
6. 常见异常处理
	1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/运维，监控与异常处理/Ocean Base 错误信息概述\|Ocean Base 错误信息概述]]，；
	2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/运维，监控与异常处理/问题排查概述：副本迁移问题排查\|问题排查概述：副本迁移问题排查]]，；
8. 灾难恢复

#### 4 查询
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询磁盘使用情况\|查询磁盘使用情况]]，；
2. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/查询 OB 资源分配情况\|查询 OB 资源分配情况]]，查询 OB 集群的 OBServer ，及租户的资源使用情况；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询数据库信息\|查询数据库信息]]，；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查看当前使用的数据库及用户名\|查看当前使用的数据库及用户名]]，；
5. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OB 集群副本分布情况\|查询 OB 集群副本分布情况]]，；
6. [[15_OceanBase/20_OceanBase 常用 Sql 语句/Oceanase 查询数据库对象情况\|Oceanase 查询数据库对象情况]]，；
7. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询服务版本\|查询服务版本]]，；


#### 5 获取，拷贝，分析 OB 相关日志
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/分析 observer 日志\|分析 observer 日志]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_1\|拷贝 Oceanbase 日志文件_1]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_2\|拷贝 Oceanbase 日志文件_2]]，ZJGY 拷贝 OBServer 日志文件到本地；；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 OMS 日志\|获取 OMS 日志]]，；
5. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 ODC 日志\|获取 ODC 日志]]，；


#### 6 其他
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OB 常用查询\|OB 常用查询]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/sql_plan_monitor\|15_OceanBase/20_OceanBase 常用 Sql 语句/sql_plan_monitor]]，；

#### 7 
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 系统视图概述_V4.x\|OceanBase 系统视图概述_V4.x]]，；
2. [[15_OceanBase/99_OceanBase 内部表介绍\|99_OceanBase 内部表介绍]]，；

### 参考文档




```sql
-- log盘满：查询表__all_virtual_server_clog_stat，清除较老的日志
SELECT svr_min_log_timestamp FROM oceanbase.__all_virtual_server_clog_stat WHERE zone_status='ACTIVE';
```



