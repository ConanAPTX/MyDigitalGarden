---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行备份备份数据的恢复_实践/","dgPassFrontmatter":true}
---



### OceanBase 执行备份备份数据的恢复_实践
#### 1 说明
该文档创建于 2024-07-24，用于在 ob_gy 集群中恢复 gydc_ohy_other 租户，恢复后的租户名称为：other_backup；

#### 2 执行步骤
 1.`创建资源池`
```sql
-- 创建租户规格  
CREATE RESOURCE UNIT restore_30c50g max_cpu 30, max_memory 53687091200, max_iops 1024000, max_disk_size 53687091200, max_session_num 128, 
	MIN_CPU=30, MIN_MEMORY=53687091200, MIN_IOPS=1024000;  
-- 创建资源池  
CREATE RESOURCE POOL restore_pool unit = 'restore_30c50g', unit_num = 1, zone_list = ('zone3');  
```

2.`加载备份文件`
```sql
-- 1.执行 SHOW RESTORE PREVIEW 来获取恢复数据的路径 
obclient> SHOW RESTORE PREVIEW;

-- 2.加载全量 + 增量备份文件  
alter system add restore source 'oss://ob-backup/ob_gy/1/incarnation_1/1022/data/backup_set_1170_full_20240720?host=oss-cn-deqing-zjzfy01-d02-a.cloud.zj.gov.cn&access_id=xxxxxx&access_key=xxxxxx';  
alter system add restore source 'oss://ob-backup/ob_gy/1/incarnation_1/1022/data/backup_set_1171_inc_20240721?host=oss-cn-deqing-zjzfy01-d02-a.cloud.zj.gov.cn&access_id=xxxxxx&access_key=xxxxxx';  
alter system add restore source 'oss://ob-backup/ob_gy/1/incarnation_1/1022/data/backup_set_1172_inc_20240722?host=oss-cn-deqing-zjzfy01-d02-a.cloud.zj.gov.cn&access_id=xxxxxx&access_key=xxxxxx';  
  
-- 3.加载日志备份文件  
alter system add restore source 'oss://ob-backup/ob_gy/1/incarnation_1/1022/clog/13?host=oss-cn-deqing-zjzfy01-d02-a.cloud.zj.gov.cn&access_id=xxxxxx&access_key=xxxxxx';  
/*
host=oss-cn-deqing-zjzfy01-d02-a.cloud.zj.gov.cn&access_id=xxxxxx&access_key=xxxxxx：登录 OSS 的账号密码；
*/

-- 如果输入错误，您可执行下述语句来撤销之前的输入： 
ALTER SYSTEM CLEAR RESTORE SOURCE;
```

3.`发起恢复任务`
```sql
-- 发起恢复任务  
alter system restore other_backup from gydc_ohy_other until '2024-07-22 10:30:00' with 'backup_cluster_name=ob_gy&backup_cluster_id=1&pool_list=restore_pool';
/* 参数说明：
	other_backup：恢复后的租户名称；
	gydc_ohy_other：原租户的名称；
	'2024-07-22 10:30:00'：截止的时间；
	backup_cluster_name：集群名称；
	backup_cluster_id：集群 ID；
	pool_list：恢复租户的指定的资源池；
*/
```


### 参考文档



