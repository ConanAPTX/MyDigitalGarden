---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/","dgPassFrontmatter":true}
---


### OceanBase 常用 Sql 语句
#### 1 日常巡检
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/日常巡检/检查 NTP 偏移量\|检查 NTP 偏移量]]，；

#### 2 查询
1. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/查询 OB 资源分配情况\|查询 OB 资源分配情况]]，查询 OB 集群的 OBServer ，及租户的资源使用情况；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查看当前使用的数据库及用户名\|查看当前使用的数据库及用户名]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OB 集群副本分布情况\|查询 OB 集群副本分布情况]]，；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询磁盘使用情况\|查询磁盘使用情况]]，；
5. [[15_OceanBase/20_OceanBase 常用 Sql 语句/Oceanase 查询数据库对象情况\|Oceanase 查询数据库对象情况]]，；
6. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询服务版本\|查询服务版本]]，；
7. [[15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OB 集群内 OBServer 资源分配情况\|查询 OB 集群内 OBServer 资源分配情况]]，；
8. [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/通过黑屏客户端连接 OB 数据库\|通过黑屏客户端连接 OB 数据库]]，；
9. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/查询，终止租户会话\|查询，终止租户会话]]，查询连接信息；
10. [[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/查询，终止租户会话\|查询，终止租户会话]]，*查询数据库连接情况*；包括物理连接；物理连接主要是指网络连接部分；
11.  [[15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/逻辑连接\|逻辑连接]]，包括：展示全部 Session，展示 Session 详细状态，终止 Server Session；查询 OBProxy 连接信息；

[[15_OceanBase/20_OceanBase 常用 Sql 语句/OB 常用查询\|OB 常用查询]]，；


#### 3 获取，拷贝，分析 OB 相关日志
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/分析 observer 日志\|分析 observer 日志]]，；
2. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_1\|拷贝 Oceanbase 日志文件_1]]，；
3. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_2\|拷贝 Oceanbase 日志文件_2]]，浙江高院拷贝 OBServer 日志文件到本地；；
4. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 OMS 日志\|获取 OMS 日志]]，；
5. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 ODC 日志\|获取 ODC 日志]]，；
```sql
SHOW VARIABLES LIKE 'ob_query_timeout';  
SHOW VARIABLES LIKE 'ob_trx_timeout';  
SHOW VARIABLES LIKE 'ob_trx_idle_timeout';  
  
BEGIN; -- 开启事务  
SET SESSION ob_query_timeout = 10000000000; -- 用于设置 SQL 最大执行时间，单位为微秒；  
SET SESSION ob_trx_timeout = 100000000000; -- 用于设置事务超时（未提交）时间，单位为微秒；  
SET SESSION ob_trx_idle_timeout = 120000000000; -- 用于设置事务空闲超时时间，即事务中两条语句之间的执行间隔超过该值时超时，单位为微秒；  
ckd_birth_tb_eh_hz;  
END; -- 提交事务  
ROLLBACK; -- 回滚事务
```
  
```sql
-- 查询系统任务信息  
select * from oceanbase.__all_virtual_sys_task_status;  
select start_time, task_type, task_id, svr_ip, svr_port, tenant_id, comment, is_cancel  
from oceanbase.__all_virtual_sys_task_status  
where tenant_id = 1014;  
/*  
	start_time：开始时间；  
	task_type：任务类型；  
	task_id：任务 ID；  
	svr_ip：IP 地址；  
	svr_port：端口号；  
	tenant_id：租户 ID；  
	comment：注释；  
	is_cancel：是否取消；  
*/  
```

  



### 参考文档



