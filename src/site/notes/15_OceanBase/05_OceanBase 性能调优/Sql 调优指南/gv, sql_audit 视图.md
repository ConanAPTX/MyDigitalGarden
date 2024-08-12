---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/gv, sql_audit 视图/","dgPassFrontmatter":true}
---

### gv$sql_audit 视图
#### 1 gv$sql_audit 表概述
该视图为`全局 SQL 审计表`，基于虚拟表 *\_\_all_virtual_sql_audit* 的视图， 该虚拟表对应的数据存放在一个可配置的内存空间中；由于存放这些记录的内存是有限的，因此到达一定内存使用量，会触发淘汰；

可以用来查看每次请求客户端来源，执行 server 信息，执行状态信息，等待事件及执行各阶段耗时等；是按照租户拆分的，除了系统租户，其他租户不能跨租户查询；


#### 2 sql_audit 相关设置
##### 2.1 开启 SQL Audit_集群级

```sql
-- 使用 root@sys 登录 sys 租户
mysql -h10.x.x.x -P2883 -uroot@sys#obcluster -p

--  查询集群 SQL Audit 相关参数
SHOW PARAMETERS LIKE 'enable_perf_event';
SHOW PARAMETERS LIKE 'enable_sql_audit';

ALTER SYSTEM SET enable_perf_event=true;  -- 开启
ALTER SYSTEM SET enable_sql_audit = true；  /*开启 sql_audit*/

ALTER SYSTEM SET enable_sql_audit = false;	/*关闭 sql_audit*/
ALTER SYSTEM SET enable_perf_event=false; -- 关闭

-- 设置 sql_audit 内存上限。默认内存上限为 3 G，可设置范围为 [64M,+∞]
ALTER SYSTEM SET sql_audit_memory_limit = '3G';
```


##### 2.2 开启 SQL Audit_租户级
```sql
-- 登录业务租户(MySQL 模式或 Oracle 模式)
obclient -h10.x.x.x -P2883 -uroot@mytenant#obcluster -p

-- 查询租户 SQL Audit 相关变量
SHOW VARIABLES LIKE '%ob_enable_sql_audit%';

-- 设置租户 SQL Audit 相关变量
SET GLOBAL ob_enable_sql_audit=ON; -- 开启
SET GLOBAL ob_enable_sql_audit=OFF; -- 关闭

SHOW VARIABLES LIKE '%ob_enable_sql_audit%';
```



#### 3 清理 SQL AUDIT

##### 3.1 sql_audit 自动淘汰机制

> [!NOTE] sql_audit 淘汰机制： 
> 1. *sql_audit 每隔 1s 会检测后台任务并根据以下标准决定是否淘汰*：
> 	1. sql_audit 内存最大可使用上限为 avail_mem_limit = min (OBServer 可使用内存 * 10%，sql_audit_memory_limit)；
> 		1. 当 avail_mem_limit 在 [64M, 100M] 范围内时, 内存使用值达到 avail_mem_limit－20M 时触发淘汰；
> 		2. 当 avail_mem_limit 在 [100M, 5G] 范围内时, 内存使用值达到 availmem_limit * 0.8 时触发淘汰；
> 		3. 当 avail_mem_limit 在 [5G, +∞] 范围内时, 内存使用值达到 availmem_limit－1G 时触发淘汰；
> 	2. 当 sql_audidt 记录数超过 900 万条时，触发淘汰；
> 2. *sql_audit 根据以下标准决定是否停止淘汰*：
> 	1. 如果是达到内存上限触发的淘汰，则：
> 		1. 当 avail_mem_limit 在 [64M, 100M] 时, 内存使用淘汰值达到 avail_mem_limit－40M 时停止淘汰;
> 		2. 当 avail_mem_limit 在 [100M, 5G] 时, 内存使用淘汰值达到 availmem_limit * 0.6 时停止淘汰；
> 		3. 当 avail_mem_limit 在 [5G, +∞] 时, 内存使用淘汰值达到 availmem_limit－2G 时停止淘汰；
> 	2. 如果是达到记录数上限触发的淘汰，则淘汰值达到 800 万行记录时停止淘汰；

| `序号` | `区间` | `淘汰触发条件` | `停止淘汰触发条件` | 备注 |
| :-------------: | :---------- | :------------ |:------------ |:------------ |
| sql_audit 内存最大可使用上限<br>（avail_mem_limit） | [64M, 100M] |  avail_mem_limit - 20M |  avail_mem_limit - 40M |  |
|  | [100M, 5G] | availmem_limit * 0.8 | availmem_limit * 0.6  |  |
|  | [5G, +∞] | availmem_limit - 1G | availmem_limit－2G |  |
| sql_audidt记录数上限 | / | 超过 900w 条记录 | 达到 800w 条记录 |  |
   









 

  





##### 3.2 手动清理 sql_audit

> [!NOTE] `手动清理 sql_audit 注意事项：`
> 1. 适用版本：V2.2.x、V3.1.x、V3.2.x；
> 2. 当某一条记录正在被访问，而 FLUSH 命令也恰巧要淘汰这一条记录，淘汰操作会等待，直到访问结束。下次再次访问 `gv$sql_audit` 或 `v$sql_audit` 时，对应的记录不存在；

```sql
/*
清理 SQL AUDIT 的命令如下:
	-- ALTER SYSTEM FLUSH SQL AUDIT [tenant_list] [GLOBAL]
参数介绍：
	tenant_list 和 GLOBAL 为可选字段；
	如果在 tenant_list 中指定租户，则只删除指定租户的 SQL AUDIT。否则默认会删除所有租户的相关 SQL AUDIT；
    如果指定了 GLOBAL，会删除所有 OBServer 上该租户的 SQL AUDIT。否则仅删除本机中相关租户的 SQL AUDIT；
*/ 
```


### 参考文档：
1.  *gv$sql_audit / GV\$OB_SQL_AUDIT 介绍*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945961)，；[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699453)，；
	1. 从 V4.0.0 版本开始引入 `v$OB_SQL_AUDIT` 视图，该视图用于展示当前 OBServer 节点的每一次 SQL 请求的来源、执行状态等统计信息；
	2. *gv\$sql_audit /  GV$OB_SQL_AUDIT*：[SYS_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219776)，;
		1. [MySql_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356331)，[MySql_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219838)，；
		2. [Oracle_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356209)，[Oracle_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219093)，；
2. [如何清理 SQL AUDIT](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000133?back=kb)，适用版本：V2.2.x、V3.1.x、V3.2.x；
