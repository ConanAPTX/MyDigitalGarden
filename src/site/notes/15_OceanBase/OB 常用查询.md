---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/OB 常用查询/","dgPassFrontmatter":true}
---

### OB 常用查询

#### 1 查询 OB 集群内 OBServer 资源分配情况  

```sql
-- 以下相关查询适用于 3.x 版本；(使用 root 用户登录数据库的 sys 租户)  
-- 1. __all_virtual_server_stat，内部表，用于查询 OB Server 资源情况；  
select * from oceanbase.__all_virtual_server_stat limit 1\G  
  
-- 2. 查看集群资源由各个节点的聚合情况  
select zone, concat(svr_ip, ':', svr_port) observer, cpu_capacity, cpu_total, cpu_assigned, cpu_assigned_percent, mem_capacity, mem_total, mem_assigned, 
	mem_assigned_percent, unit_Num, round('load', 2) 'load', round('cpu_weight', 2) 'cpu_weight', round('memory_weight', 2) 'mem_weight', leader_count  
from __all_virtual_server_stat  
order by zone, svr_ip;  
  
-- 3. 查询 OB 集群内每个 OBServer 资源分配情况，包括CPU，内存，及磁盘使用情况；  
SELECT a.zone,concat(a.svr_ip,':',a.svr_port) observer, a.cpu_total, a.cpu_assigned, (a.cpu_total - a.cpu_assigned) cpu_free, cpu_assigned_percent,  
	round(a.mem_total/1024/1024/1024,2) AS mem_total_gb, round(a.mem_assigned/1024/1024/1024,2) mem_assign_gb, round((a.mem_total - a.mem_assigned)/1024/1024/1024,2) mem_free_gb,mem_assigned_percent,  
	round(a.disk_total/1024/1024/1024,2) AS disk_total_gb, round(a.disk_assigned/1024/1024/1024,2) AS disk_assigned_gb  
FROM oceanbase.__all_virtual_server_stat a  
JOIN oceanbase.__all_server b ON (a.svr_ip = b.svr_ip AND a.svr_port = b.svr_port)  
ORDER BY a.zone, a.svr_ip;  
  
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
where a.svr_ip = b.svr_ip order by zone,cpu_assigned_percent desc;
```



### 参考文档



