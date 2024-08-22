---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理资源/查询 OceanBase 资源分配情况/","dgPassFrontmatter":true}
---

### 查询 OceanBase 资源分配情况

#### 1 查看 OB 集群内 OBServer 资源情况
以 zone 维度查看资源使用情况；
注意：利用 `__all_virtual_server_stat` 表查询内存分配状况，查询结果会与 OCP 查询结果有较大差异，详细结果请参考：[[15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_server_stat\|oceanbase.__all_virtual_server_stat]]，；

```sql  
obclient -h10.10.10.1 -P2881 -uroot@sys -p -A         -- 使用 root 用户登录数据库的 sys 租户 

-- 以下相关查询适用于 3.x 版本；(使用 root 用户登录数据库的 sys 租户)
-- 1. __all_virtual_server_stat，内部表，用于查询 OB Server 资源情况；
select * from oceanbase.__all_virtual_server_stat limit 1\G  

-- 2. 查看集群资源由各个节点的聚合情况
select zone, concat(svr_ip, ':', svr_port) observer,
  cpu_capacity, cpu_total, cpu_assigned, cpu_assigned_percent, mem_capacity, mem_total, mem_assigned, mem_assigned_percent,
  unit_Num, round('load', 2) 'load', round('cpu_weight', 2) 'cpu_weight', round('memory_weight', 2) 'mem_weight', leader_count
from __all_virtual_server_stat
order by zone, svr_ip;

-- 3. 查询 OB 集群内每个 OBServer 资源分配情况，包括CPU，内存，及磁盘使用情况；
SELECT a.zone,concat(a.svr_ip,':',a.svr_port) observer, 
	a.cpu_total, a.cpu_assigned, (a.cpu_total - a.cpu_assigned) cpu_free, cpu_assigned_percent,  
	round(a.mem_total/1024/1024/1024,2) AS mem_total_gb, round(a.mem_assigned/1024/1024/1024,2) mem_assign_gb, 
	round((a.mem_total - a.mem_assigned)/1024/1024/1024,2) mem_free_gb,mem_assigned_percent,  
	round(a.disk_total/1024/1024/1024,2) AS disk_total_gb, round(a.disk_assigned/1024/1024/1024,2) AS disk_assigned_gb  
FROM oceanbase.__all_virtual_server_stat a  
JOIN oceanbase.__all_server b ON (a.svr_ip = b.svr_ip AND a.svr_port = b.svr_port)  
ORDER BY a.zone, a.svr_ip;  
-- 如果某个zone中所有server的某项指标(cpu_assigned_percent, mem_assigned_percent) 都比较高(>90)，后续加租户或扩租户资源可能会因资源不够失败，可考虑集群扩容；


-- 4. 与上面的 SQL 基本类似，仅做了数据格式的处理，使查询结果与 OCP 查询结果类似；
SELECT a.zone,concat(a.svr_ip,':',a.svr_port) observer,  
	concat(round(a.mem_assigned/1024/1024/1024,2),'/',round(a.mem_total/1024/1024/1024,2),'G',' ',mem_assigned_percent,'%') as mem,  
	concat(a.cpu_assigned,'/',a.cpu_total,'核',' ',cpu_assigned_percent,'%') as CPU,  
	concat(round(a.disk_in_use/1024/1024/1024,2),'/',round(a.disk_total/1024/1024/1024,2),'G',' ',round(a.disk_in_use/a.disk_total*100,0),'%') as disk  
FROM oceanbase.__all_virtual_server_stat a  
JOIN oceanbase.__all_server b ON (a.svr_ip = b.svr_ip AND a.svr_port = b.svr_port)  
ORDER BY a.zone, a.svr_ip;  

-- 5.  
select /*+READ_CONSISTENCY(WEAK), QUERY_TIMEOUT(100000000)*/ b.zone, a.svr_ip, a.cpu_total, a.cpu_assigned cpu_ass,  
	a.cpu_assigned_percent cpu_ass_percent, round(a.mem_total/1024/1024/1024, 2) as mem_total, round(a.mem_assigned/1024/1024/1024, 2) mem_ass,  
	round((a.mem_total-a.mem_assigned)/1024/1024/1024, 2) as mem_free, a.mem_assigned_percent mem_ass_percent  
from oceanbase.__all_virtual_server_stat a, oceanbase.__all_server b  
where a.svr_ip = b.svr_ip 
order by zone,cpu_assigned_percent desc;
```  


##### 1.1 GV$OB_SERVERS
[[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/查询磁盘使用情况_V4.x#1.1 V4.0.0 及以上版本\|查询磁盘使用情况_V4.x#1.1 V4.0.0 及以上版本]]


#### 2 以 pool 维度查看资源使用情况
`gv$unit` 视图，用于展示集群中所有 Unit 的 Meta 信息，相关信息来自 `oceanbase.__all_unit`，`oceanbase.__all_unit_config`，`oceanbase.__all_resource_pool`，`oceanbase.__all_tenant`  内部表；

更多系统视图 `gv$unit` 的说明信息请参见 [gv$unit](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945225)，； 从 V4.0.0 版本开始废弃 *gv$unit* 视图；

```sql
-- 1.使用 root 用户登录数据库的 sys 租户

-- 查看集群内所有租户所使用的资源单元的配置信息
SELECT * FROM oceanbase.gv$unit;
SELECT * FROM oceanbase.gv$unit where tenant_name='my_test'\G;
```

```sql
-- 查看某个租户的所有资源池中资源单元的个数
SELECT resource_pool_id, name, unit_count, unit_config_id FROM oceanbase.__all_resource_pool WHERE tenant_id='1001';
	-- unit_count 即表示对应资源池中资源单元的个数；
```

```sql
-- 以下相关查询适用于 3.x 版本；(使用 root 用户登录数据库的 sys 租户)
-- 2. 
SELECT /*+read_consistency(weak) */ a.unit_id, concat(a.svr_ip,':',a.svr_port) as observer, a.zone, a.tenant_id, b.tenant_name, a.min_cpu, a.max_cpu, 
	round(a.min_memory/1024/1024/1024,2) as min_memory_gb, round(a.max_memory/1024/1024/1024,2) as max_memory_gb  
FROM oceanbase.gv$unit as a  
INNER JOIN oceanbase.gv$tenant b ON a.tenant_id=b.tenant_id  
where a.tenant_id='1001'  
order by a.tenant_id,a.zone;  

-- 3. 查看租户对应的 Unit 数量，Zone 信息
SELECT t1.*,t2.*,t3.*,t4.*  
FROM oceanbase.__all_resource_pool t1  
JOIN oceanbase.__all_unit_config t2 ON (t1.unit_config_id=t2.unit_config_id)  
JOIN oceanbase.__all_unit t3 ON (t1.`resource_pool_id` = t3.`resource_pool_id`)  
LEFT JOIN oceanbase.__all_tenant t4 ON (t1.tenant_id=t4.tenant_id)  
ORDER BY t1.`resource_pool_id`,t2.`unit_config_id`,t3.unit_id;  

-- 4. 与上面的 SQL 基本一致，仅查询了部分字段
SELECT t4.tenant_id,t4.tenant_name,concat(t3.svr_ip,':',t3.`svr_port`) as observer,t3.unit_id,t3.zone,t3.status  
	,t1.gmt_create,t1.resource_pool_id,t1.name as resource_pool_name,t1.unit_count,t1.unit_config_id,t1.zone_list  
	,t2.name as unit_config_name,t2.max_cpu
	,concat(t2.min_cpu,'C',round(t2.max_memory/1024/1024/1024,0),'G ~ ',t2.max_cpu,'C',round(t2.min_memory/1024/1024/1024,0),'G') as cpu_mem
	,concat(min_iops,'/',max_iops) as iops  
FROM oceanbase.__all_resource_pool t1  
JOIN oceanbase.__all_unit_config t2 ON (t1.unit_config_id=t2.unit_config_id)  
JOIN oceanbase.__all_unit t3 ON (t1.`resource_pool_id` = t3.`resource_pool_id`)  
LEFT JOIN oceanbase.__all_tenant t4 ON (t1.tenant_id=t4.tenant_id)  
where t1.tenant_id = 1001  
ORDER BY t1.`resource_pool_id`,t2.`unit_config_id`,t3.unit_id  
limit 10\G  
```  
  
```sql  
-- 以下相关查询适用于 4.x 版本；
-- 1. 
SELECT t1.name resource_pool_name, t2.`name` unit_config_name, t2.max_cpu, t2.min_cpu, t2.memory_size/1024/1024/1024 memory_size,  
	t3.unit_id, t3.zone, concat(t3.svr_ip,':',t3.`svr_port`) observer, t4.tenant_id, t4.tenant_name  
FROM oceanbase.__all_resource_pool t1  
JOIN oceanbase.__all_unit_config t2 ON (t1.unit_config_id=t2.unit_config_id)  
JOIN oceanbase.__all_unit t3 ON (t1.`resource_pool_id` = t3.`resource_pool_id`)  
LEFT JOIN oceanbase.__all_tenant t4 ON (t1.tenant_id=t4.tenant_id)  
ORDER BY t1.`resource_pool_id`, t2.`unit_config_id`, t3.unit_id;  
```




### 参考文档：
1. *(G)V$OB_SERVERS*：*SYS 租户*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820318)，；*MySql 租户*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820467)，；