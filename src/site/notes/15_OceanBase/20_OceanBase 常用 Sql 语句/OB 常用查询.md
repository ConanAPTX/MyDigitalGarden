---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/OB 常用查询/","dgPassFrontmatter":true}
---

### OB 常用查询




```sql
-- 查看当前所有宏块的信息  
-- 适用版本：V3.2.x 及之前版本  
SELECT * FROM oceanbase.__all_virtual_partition_sstable_macro_info;  
  
-- 查看当前所有宏块的信息_V4.0.0 版本  
SELECT * FROM oceanbase.__all_virtual_tablet_sstable_macro_info;  
```


```sql 
-- 查询 COMPACTION 的诊断信息，适用版本：V3.2 版本  
select * from oceanbase.__all_virtual_compaction_diagnose_info;  
  
-- 查询 COMPACTION 的诊断信息，适用版本：V4.0.0 版本  
select * from oceanbase.__all_virtual_compaction_diagnose_info;  
select * from oceanbase.GV$OB_COMPACTION_DIAGNOSE_INFO;  
/* GV$OB_COMPACTION_DIAGNOSE_INFO 视图字段详情：  
	SVR_IP：服务器 IP 地址；  
	SVR_PORT：服务器端口号；  
	TENANT_ID：租户 ID；  
	TYPE：compaction 的类型；  
	MINI：转储/L0 compaction，Memtable 通过转储变成 SSTable；  
	MAJOR：合并；  
	MINI MINOR：L1 compaction，多个 Mini SSTable 合成一个；  
	BUF MINOR：生成特殊的 buf minor sstable 的 compaction；  
	LS_ID：日志流 ID；  
	TABLET_ID：数据分片 ID；  
	STATUS：任务状态；  
	CREATE_TIME：创建时间；  
	DIAGNOSE_INFO：诊断信息；  
*/  
```





```sql
-- dag 虚拟表  
select * from oceanbase.__all_virtual_dag;  
  
-- dag scheduler 虚拟表  
select * from oceanbase.__all_virtual_dag_scheduler;  
  
-- 失败 dag 记录  
select * from oceanbase.__all_virtual_dag_warning_history;
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



