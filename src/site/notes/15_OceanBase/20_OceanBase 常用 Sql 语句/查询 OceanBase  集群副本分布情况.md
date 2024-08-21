---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OceanBase  集群副本分布情况/","dgPassFrontmatter":true}
---


### 文档名称
#### 1 查询 OBServer 的分区情况
从 V4.0.0 版本开始废弃 *gv$partition* 视图；
```sql
select count(*) from gv$partition;
select svr_ip, svr_port，count(*) as part_num from gv$partition group by svr_ip,svr_port;
```

```sql
-- 查询各台主机上的单机的 partition 数【在 SYS 租户中执行】
select svr_ip, svr_port, count(*) as part_num from oceanbase.__all_virtual_partition_table group by svr_ip,svr_port;

-- 查找租户有多少表
select svr_Ip, count(*) as tab_num from oceanbase.__all_virtual_meta_table where tenant_id=1100 and role=1 group by svr_ip;
```

```sql
-- 统计每个租户在各个 Server 上的分区数量
select svr_ip, svr_port, table_id >> 40 as tenant_id, count(*) as part_num 
from  ( 
	select * from __all_virtual_clog_stat group by table_id, partition_idx
) as vt 
group by svr_ip, table_id >> 40 
order by svr_ip, tenant_id;
```


#### 2 查询租户的分区情况
```sql
-- 查询指定租户的分区信息  
SELECT table_id, partition_idx, usec_to_time(min_log_service_ts), TIME_TO_SEC( now())-TIME_TO_SEC(usec_to_time(min_log_service_ts)) delta_time  
FROM oceanbase.__all_virtual_partition_info  
WHERE tenant_id = 1004;
```

```sql
-- 查询 OB 集群所有租户的副本数量  
SELECT /*+ READ_CONSISTENCY(WEAK) QUERY_TIMEOUT(100000000)*/ t2.tenant_name,t1.svr_ip, t1.replica_count  
FROM(  
	SELECT tenant_id,svr_ip, COUNT(*) AS replica_count  
	FROM oceanbase.__all_virtual_partition_info  
	/* WHERE tenant_id = '1008' and svr_ip = '100.64.174.102' 限定租户 */  
	GROUP BY tenant_id, svr_ip ORDER BY replica_count DESC LIMIT 30  
) t1  
left join (SELECT tenant_id, tenant_name FROM __all_tenant) t2 ON t1.tenant_id = t2.tenant_id  
ORDER BY replica_count DESC;  
  
-- 查询 OB 集群所有租户的副本数量，该语句还包括表数量（数据不一定准确）  
SELECT /*+ READ_CONSISTENCY(WEAK) QUERY_TIMEOUT(100000000)*/ t2.tenant_id, t2.tenant_name,t1.svr_ip,replica_count,t1.table_count  
FROM(  
	SELECT tenant_id,svr_ip, COUNT(*) AS replica_count, COUNT(distinct(table_id)) AS table_count  
	FROM oceanbase.__all_virtual_partition_info  
	/* WHERE tenant_id = '1008' and svr_ip = '100.64.174.102' 限定租户 */  
	GROUP BY tenant_id, svr_ip ORDER BY replica_count DESC LIMIT 30  
) t1  
left join (SELECT tenant_id, tenant_name FROM oceanbase.__all_tenant) t2 ON t1.tenant_id = t2.tenant_id  
ORDER BY replica_count DESC;  
```



#### 3 查看业务租户内部所有 leader 副本的位置  
查看指定租户所有副本的角色，即是 leader 副本，或是 follower 副本；

从 V4.0.0 版本开始废弃 *gv\$partition* 视图，该详细情况：[gv\$partition](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356350)，展示集群中所有的 partition meta 信息；
从 V4.0.0 版本开始废弃 *gv$database* 视图；

```sql  
-- 查看业务租户内部所有 leader 副本的位置(使用 root 用户登录数据库的 sys 租户)  
-- 1. 查询指定租户所有的副本的情况，包括副本角色，数据大小，数据行数等，
select t5.tenant_name,t4.database_name,t3.tablegroup_name,/*t1.table_id,*/ t1.table_name, t2.partition_id,  
	case t2.role when 1 then 'leader' when 2 then 'follower' else '未知' end as role,  
	t2.zone,concat(t2.svr_ip,':',t2.svr_port) observer, round(t2.data_size/1024/1024) data_size_mb, t2.row_count  
from oceanbase.__all_virtual_table as t1  
join oceanbase.gv$partition as t2 on (t1.tenant_id = t2.tenant_id and t1.table_id = t2.table_id)  
left join oceanbase.__all_tablegroup as t3 on (t1.tenant_id = t3.tenant_id and t1.tablegroup_id = t3.tablegroup_id)  
join oceanbase.gv$database as t4 on (t1.tenant_Id = t4.tenant_id and t1.database_id = t4.database_id)  
join oceanbase.__all_tenant as t5 on (t1.tenant_id = t5.tenant_id)  
where t5.tenant_id = 1008  and t2.role = 1
order by t5.tenant_name,t4.database_name,t1.table_id,t2.zone,t3.tablegroup_name,t2.partition_id; 

-- 该 SQL 语句与上面的基本一致，仅作为参考备用；
select t5.tenant_name,t4.database_name,t3.tablegroup_name,t1.table_id,t1.table_name,t2.partition_id,t2.role,t2.zone,
	concat(t2.svr_ip,':',t2.svr_port) observer,round(t2.data_size / 1024 / 1024) data_size_mb,t2. row_count
from oceanbase.__all_virtual_table t1 -- 表的基础信息，可查看虚拟表
join oceanbase.gv$partition t2 on (t1.tenant_id = t2.tenant_id and t1.table_id = t2.table_id) -- 集群中所有的partition meta信息
left join __all_tablegroup t3 on (t1.tenant_id = t3.tenant_id and t1.tablegroup_id = t3.tablegroup_id) -- 表组信息
join oceanbase.__all_database t4 on (t1.tenant_Id = t4.tenant_id and t1.database_id = t4.database_id) -- 库信息
join oceanbase.__all_tenant t5 on (t1.tenant_id = t5.tenant_id)  -- 租户信息
where t5.tenant_id = 1020 and t2.role = 1  -- role=1为leader; role=2为follower
order by t5.tenant_name,t4.database_name,t3.tablegroup_name,t2.partition_id;
```






### 参考文档



