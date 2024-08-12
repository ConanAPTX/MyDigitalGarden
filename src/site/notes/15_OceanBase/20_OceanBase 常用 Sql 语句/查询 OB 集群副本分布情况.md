---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OB 集群副本分布情况/","dgPassFrontmatter":true}
---


### 查询 OB 集群副本分布情况
#### 1 查询租户的副本数  

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


```sql
-- 查询指定租户的分区信息  
SELECT table_id, partition_idx, usec_to_time(min_log_service_ts), TIME_TO_SEC( now())-TIME_TO_SEC(usec_to_time(min_log_service_ts)) delta_time  
FROM oceanbase.__all_virtual_partition_info  
WHERE tenant_id = 1004;
```



### 参考文档



