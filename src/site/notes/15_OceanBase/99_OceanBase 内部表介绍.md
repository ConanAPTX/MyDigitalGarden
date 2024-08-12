---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_OceanBase 内部表介绍/","dgPassFrontmatter":true}
---


### OceanBase 内部表介绍
 *SYS 租户*：[V4.3.2]()，；*MySql 租户*：[V4.3.2]()，；*Oracle 租户*：[V4.3.2]()，；


#### 1 租户
##### 1.1 字典视图_Outline
 1. [[15_OceanBase/99_内部表介绍/字典视图_Outline 信息\|字典视图_Outline 信息]]，；
	 1. *GV$OUTLINE*：展示当前租户的 Outline 信息；
	 2. 【*从 V4.0.0 版本开始引入*】
		 1. *oceanbase.DBA_OB_OUTLINES*：展示本租户的执行计划 Outline 信息；
		 2.  *oceanbase.DBA_OB_CONCURRENT_LIMIT_SQL*：展示本租户的限流规则 Outline 相关信息；
		 3. *oceanbase.DBA_OB_OUTLINE_CONCURRENT_HISTORY*：展示本租户的执行计划和限流规则 Outline 历史信息；

##### 1.2 字典视图_备份恢复相关
1. 【_从 V2.2.76 版本已存在_】
	1. [[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_1000\|系统视图_日志备份相关_1000]]，；
		1. `oceanbase.CDB_OB_BACKUP_PROGRESS`：展示数据库备份任务的进度；
{ #5c26a1}

	2. *oceanbase.CDB_OB_BACKUP_SET_OBSOLETE*：展示了哪些备份集已过期；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376358)，；
	3. *oceanbase.CDB_OB_BACKUP_SET_EXPIRED*：用于展示哪些备份集的数据已过期；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376342)，；
	4. `oceanbase.CDB_OB_BACKUP_SET_DETAILS`：展示备份集的详细信息；
		1.  *SYS 租户*：，；*MySql 租户*：[V2.2.77](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376340)，[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945439)，；*Oracle 租户*：，；
	5. *oceanbase.CDB_OB_BACKUP_TASK_CLEAN_HISTORY*：用于记录被清理掉的备份信息；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376345)，；
	6. *oceanbase.CDB_OB_BACKUP_VALIDATION_JOB*：用于展示备份校验进度，用于在 OCP 上展示备份校验的情况，只保留正在执行的任务；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376349)，；
	7. *oceanbase.CDB_OB_BACKUP_VALIDATION_JOB_HISTORY*：展示备份校验进度，用于在 OCP 上展示备份校验的情况，保留所有历史任务；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376350)，；
	8. *oceanbase.CDB_OB_TENANT_BACKUP_VALIDATION_TASK*：用于展示租户级别的备份校验任务；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376351)；
	9. *oceanbase.CDB_OB_BACKUP_VALIDATION_TASK_HISTORY*：是 oceanbase.`CDB_OB_TENANT_BACKUP_VALIDATION_TASK` 的历史表；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376352)，；
	10. *oceanbase.CDB_OB_BACKUP_BACKUP_ARCHIVELOG_SUMMARY*：用于展示已经完成的备份备份日志归档的 Round 的信息；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376353)，；
	11. *oceanbase.CDB_OB_BACKUP_BACKUPSET_TASK*：用于展示正常进行的备份备份基线任务的信息，按 Job 展示对应的 Task；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376355)，；
	12. *oceanbase.CDB_OB_BACKUP_BACKUPSET_TASK_HISTORY*：用于展示已完成的备份备份基线任务的信息；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376354)，；
	13. *oceanbase.CDB_OB_BACKUP_BACKUPSET_JOB*：用于展示备份备份基线任务的信息，一条 SQL 即对应一个 Job；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376357)，；
	14. *oceanbase.CDB_OB_BACKUP_BACKUPSET_JOB_HISTORY*：用于展示发起过备份备份基线的 Job 的历史信息；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376356)，；
2. 【*从 V2.2.77 版本开始引入*】：
	1.  [[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_010\|系统视图_日志备份相关_010]]，详细介绍以下【视图】的信息；
		1. *oceanbase.CDB_OB_BACKUP_ARCHIVELOG*：用于展示正在备份的日志状态；
		3. *oceanbase.CDB_OB_BACKUP_ARCHIVELOG_SUMMARY*：用于展示日志备份的任务信息；【_从 V2.2.76 版本已存在_】
		4. *oceanbase.CDB_OB_BACKUP_ARCHIVELOG_PROGRESS*：用于展示单个 Partition Group 的备份进度信息；【_从 V2.2.76 版本已存在_】
	2. *oceanbase.CDB_OB_BACKUP_JOB_DETAILS*：用于展示备份任务的详细信息；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376339)，；
	3. [[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_999\|系统视图_日志备份相关_999]]，；
		1. *oceanbase.CDB_OB_BACKUP_PIECE_FILES*：用于展示备份集中 Piece 的状态；
		2. *oceanbase.CDB_OB_BACKUP_BACKUPPIECE_JOB*：用于展示备份备份 Piece 的任务进度；
		3. *oceanbase.CDB_OB_BACKUP_BACKUPPIECE_JOB_HISTORY*：用于展示已完成的备份一组备份 Piece 的任务的历史信息表；
		4. *oceanbase.CDB_OB_BACKUP_BACKUPPIECE_TASK*：用于展示备份某个备份 Piece 的任务详细信息；
		5. *oceanbase.CDB_OB_BACKUP_BACKUPPIECE_TASK_HISTORY*：用于展示已完成的备份某个备份 Piece 的任务的历史信息表；
	4. *oceanbase.CDB_OB_BACKUP_CLEAN_HISTORY*：租户级别备份数据清理的历史信息表；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376344)，；
	5. *oceanbase.CDB_OB_BACKUP_BACKUP_ARCHIVELOG*：用于展示正在执行备份备份的日志 Round；[V2.2.77_MySql](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376360)，；
	6. [[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_1000\|系统视图_日志备份相关_1000]]，；
		1. *CDB_OB_RESTORE_PROGRESS*：用于展示物理恢复任务当前的执行进度；
		2. *CDB_OB_RESTORE_HISTORY*：用于展示物理恢复任务完成时的状态；
	7.  `CDB_OB_BACKUP_SET_FILES`：展示备份集的文件状态；用于展示备份集的文件状态；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051533)，；*MySql 租户*：[V2.2.77](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376362)，；*Oracle 租户*：[V2.2.77](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945465)，；
3. 【*从 V4.0.0 版本开始引入*】：
	1. `CDB_OB_ARCHIVELOG_PIECE_FILES`：用于展示备份集中 Piece 的状态；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051617)，；
	3. `CDB_OB_BACKUP_JOBS`：展示集群下所有租户的备份JOB 任务。用户发起一次备份任务，记录一条 JOB 任务记录；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051664)，；
	4. `CDB_OB_BACKUP_JOB_HISTORY`：展示集群下所有租户的备份 JOB 任务的历史记录；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051535)，；
	5. `CDB_OB_BACKUP_TASKS`：展示集群下所有租户的 TASK 级备份任务。一条 TASK 记录对应一个 backup_set 备份。TASK 任务记录和 JOB 任务记录为一一对应关系；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051415)，；
	6. `CDB_OB_BACKUP_TASK_HISTORY`：展示集群下所有租户的 TASK 级备份任务的历史记录；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051491)，；
	7. `DBA_OB_BACKUP_JOBS`：展示本租户自身的备份JOB 任务；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051615) ，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052080)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051355)，；
	8. `DBA_OB_BACKUP_JOB_HISTORY`：展示本租户自身的备份 JOB 任务的历史记录；展示本租户自身的备份 JOB 任务的历史记录；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051495) ，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052039)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051154)，；
	9. `DBA_OB_BACKUP_TASKS`：展示本租户自身的备份 JOB 任务的历史记录；展示本租户自身的备份 JOB 任务的历史记录；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051537) ，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052078)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051395)，；
	10. `oceanbase.DBA_OB_BACKUP_TASK_HISTORY`：展示租户自身的 TASK 级备份任务的历史记录；展示租户自身的 TASK 级备份任务的历史记录；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051454) ，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052072)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051162)，；
	11. `oceanbase.DBA_OB_BACKUP_SET_FILES`：展示备份集的文件状态；展示备份集的文件状态；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051566) ：；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052052)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051132)，；
	12. `oceanbase.DBA_OB_RESTORE_PROGRESS`：展示正在进行的恢复任务；展示正在进行的恢复任务；展示正在进行的恢复任务；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051527) ，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052075)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051226)：
	13. `DBA_OB_RESTORE_HISTORY`：展示已完成的恢复任务；展示已完成的恢复任务；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051462)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052042)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051175)，；
	14. `DBA_OB_ARCHIVE_DEST`：展示归档路径配置；展示归档路径配置；展示归档路径配置；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051542)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052113)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051245)，；
	15. `CDB_OB_BACKUP_PARAMETER`：展示备份参数；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051430)，；
	16. `CDB_OB_BACKUP_DELETE_JOBS`：展示租户级备份清理 JOB 任务的执行进度；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051423)，；
	17. `CDB_OB_BACKUP_DELETE_JOB_HISTORY`：展示租户下已执行的备份清理任务历史信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051422)，；
	18. `CDB_OB_BACKUP_DELETE_TASKS`：展示租户下正在执行的 backup_set/backup_piece 清理任务信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051637)，；
	19. `CDB_OB_BACKUP_DELETE_TASK_HISTORY`：展示租户下已完成的 backup_set/backup_piece 清理任务的历史信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051668)，；
	20. `CDB_OB_BACKUP_DELETE_POLICY`：展示租户下为过期清理/自动清理设置的清理策略；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051565)，；
	21. `CDB_OB_BACKUP_STORAGE_INFO`：展示租户下备份恢复使用的备份目的地相关信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051635)，；
	22. `CDB_OB_BACKUP_STORAGE_INFO_HISTORY`：展示租户下备份恢复使用的备份目的地的历史信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051667)，；
	23. `DBA_OB_BACKUP_STORAGE_INFO`：展示租户下备份恢复使用的备份目的地相关信息；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051670)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052099)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051110)，；
	24. `DBA_OB_BACKUP_STORAGE_INFO_HISTORY`：展示租户下备份恢复使用的备份目的地的历史信息；【*从 V4.0.0 版本开始引入*】
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051598)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052112)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051090)，；
	25. `DBA_OB_BACKUP_DELETE_POLICY`：展示租户下为过期清理/自动清理设置的清理策略；【*从 V4.0.0 版本开始引入*】
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051553)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052070)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051356)，；
	26. `DBA_OB_BACKUP_DELETE_JOBS`：展示租户级备份清理 JOB 任务的执行进度；【*从 V4.0.0 版本开始引入*】
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051529)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052046)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051178)，；
	27. `DBA_OB_BACKUP_DELETE_JOB_HISTORY`：展示租户下已执行的备份清理任务历史信息；【*从 V4.0.0 版本开始引入*】
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051425)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051971)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051125)，；
	28. `DBA_OB_BACKUP_DELETE_TASKS`：展示租户下正在执行的 backup_set / backup_piece 清理任务信息；【*从 V4.0.0 版本开始引入*】
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051440)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052025)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051149)，；
	29. `DBA_OB_BACKUP_DELETE_TASK_HISTORY`：展示租户下已完成的 backup_set/backup_piece 清理任务的历史信息；【*从 V4.0.0 版本开始引入*】
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051572)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052003)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051308)，；
	30. `DBA_OB_ARCHIVELOG`：展示每个路径上的日志备份状态；【*从 V4.0.0 版本开始引入*】
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051427)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052018)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051339)，；	
	31. `DBA_OB_ARCHIVELOG_SUMMARY`：展示历史以及当前所有的日志备份的状态；【*从 V4.0.0 版本开始引入*】
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051557)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052120)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051124)，；
	32. `DBA_OB_ARCHIVELOG_PIECE_FILES`：展示备份集中 Piece 的状态；【*从 V4.0.0 版本开始引入*】
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051634)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051978)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051091)，；
4. 【*从 V4.2.1 版本开始引入*】：
	1. `CDB_OB_ARCHIVELOG`：展示每个路径上的日志归档状态；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051523)，；
	2. `CDB_OB_ARCHIVELOG_SUMMARY`：展示历史以及当前所有的日志归档的状态；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051653)，；
	3. `CDB_OB_RECOVER_TABLE_JOBS`：展示表级恢复任务相关信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051605)，；
	4. `DBA_OB_RECOVER_TABLE_JOBS`：展示表级恢复任务相关信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051544)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052069)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051358)，；
	5. `CDB_OB_IMPORT_TABLE_JOBS`：展示导入任务的相关信息；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051620)，；
	6. `DBA_OB_IMPORT_TABLE_JOBS`：展示导入任务的相关信息；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051632)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052015)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051181)，；
	7. `CDB_OB_IMPORT_TABLE_TASKS`：展示表级别跨租户导入任务的记录；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051398)，；
	8. `DBA_OB_IMPORT_TABLE_TASKS`：展示表级导入任务的记录；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051436)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051960)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051147)，；
	9. `CDB_OB_RECOVER_TABLE_JOB_HISTORY`：展示表级恢复任务的结果；
		1. *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051608)，；
	10. `DBA_OB_RECOVER_TABLE_JOB_HISTORY`：展示表级恢复任务的结果；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051587)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052088)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051118)，；
	11. `CDB_OB_IMPORT_TABLE_JOB_HISTORY`：展示导入任务的结果；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051494)，；
	12. `DBA_OB_IMPORT_TABLE_JOB_HISTORY`：展示导入任务的结果；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051445)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051969)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051320)，；
	13. *CDB_OB_IMPORT_TABLE_TASK_HISTORY*：展示表级别跨租户导入任务历史记录；
		1.   *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051564)，；
	14. `DBA_OB_IMPORT_TABLE_TASK_HISTORY`：展示表级别跨租户导入任务历史记录；
		1.  *SYS 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051499)，；*MySql 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052127)，；*Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051362)，；
	15. `DBA_OB_BACKUP_PARAMETER`：展示备份参数；
		1. *Oracle 租户*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051130)，；


##### 1.3 性能视图_SQL 相关的统计信息
1. [[15_OceanBase/99_内部表介绍/SQL_PLAN_MONITOR_1232131\|SQL_PLAN_MONITOR_1232131]]：；
	1. *oceanbase.gv$SQL_PLAN_MONITOR*：展示所有 OBServer 节点慢查询的 Plan 层面的统计，每个慢查询都会有一条统计信息，同时记录该 Plan 的 Trace 信息；
	2. *oceanbase.v$SQL_PLAN_MONITOR*：慢查询的 Operation 层面的统计，每个 Operation 会有一条统计信息；
2. [[15_OceanBase/99_内部表介绍/gv$sql_audit，gv$ob_sql_audit\|gv$sql_audit，gv$ob_sql_audit]]，用于展示所有 Server 上每一次 SQL 请求的来源、执行状态等统计信息；
	1. [[15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/gv, sql_audit 视图\|gv, sql_audit 视图]]，；
	2. 该视图从 V4.0.0 版本开始视图名由 `v$sql_audit` 调整为 `v$ob_sql_audit`；
6. [[15_OceanBase/99_内部表介绍/计划缓存相关视图_001\|计划缓存相关视图_001]]，；
	1. `(G)V$PLAN_CACHE_STAT`：该视图记录当前租户在所有 Server 上的每个计划缓存整体的状态；
	2. `(G)V$PLAN_CACHE_PLAN_STAT` 视图记录了当前租户在所有 Server 上的计划缓存中缓存的每一个缓存对象的状态；
	3. `(G)V$PLAN_CACHE_PLAN_EXPLAIN` 视图：用于展示缓存在全部的 OBServer 节点中的计划缓存中的物理执行计划；
8. [[15_OceanBase/99_内部表介绍/计划缓存相关视图_002\|计划缓存相关视图_002]]，【*从 V4.0.0 版本开始引入*】；
	1. `(G)V$OB_PLAN_CACHE_STAT`：当前租户在所有 OBServer 节点上的每个计划缓存整体的状态；
	2. `(G)V$OB_PLAN_CACHE_PLAN_STAT`：展示当前租户在当前（所有）OBServer 节点上的计划缓存中缓存的每一个缓存对象的状态；
	3. `(G)V$OB_PLAN_CACHE_PLAN_EXPLAIN`：用于展示缓存在全部的 Server 中的计划缓存中的物理执行计划；
9. [[15_OceanBase/99_内部表介绍/计划缓存相关视图_0034\|计划缓存相关视图_0034]]，；
10. [GV$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051811)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 item 数目；
11. [V$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051765)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 item 数目；
12. [GV$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051807)：用于给 prepared statement 提供监控功能，包含 PS Cache 中所有 prepare statement 的基本信息；
13. [V$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051739)：用于给 prepared statement 提供监控功能，包含 PS Cache 中所有 prepare statement 的基本信息；
14. [GV$OB_PX_P2P_DATAHUB](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051779)：展示并行执行的数据传输操作的相关信息；
15. [GV$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051714)：展示所有 OBServer 节点上的 PX 线程使用情况；
16. [V$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051746)：展示 OBServer 节点上的 PX 线程使用情况；
17. [V$SQL_MONITOR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051812)：展示 `GV$SQL_PLAN_MONITOR` 的 `OTHERSTAT_X_ID`字段中数字的含义；
18. [GV$OB_FLT_TRACE_CONFIG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051718)：展示全链路诊断的诊断信息；
19. [GV$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051776)：用于展示集群中 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；
20. [V$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051775)：用于展示当前节点上 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；


#### 2 sys 租户下视图总览
##### 2.1 字典视图总览
###### 2.1.1 表和列相关
1. 1. [oceanbase.CDB_IND_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051580)，展示所有租户的索引表的列信息；
2. 1. [oceanbase.CDB_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051418)，展示所有租户的 INDEXE 信息；
3. 1. [oceanbase.CDB_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051490)，展示所有租户的 TABLE 信息；
4. 1. [oceanbase.CDB_OB_TABLEGROUP_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051595)，系统租户下展示所有租户的 TABLEGROUP、TABLE 关联关系。不展示不在 TABLEGROUP 中的 TABLE；
5. 1. [oceanbase.CDB_TAB_COLS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051656)，展示所有租户的表、视图对象的列信息；
6. 1. [oceanbase.CDB_TAB_COLS_V$](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051673)，展示所有租户的表、视图对象的列信息；
7. 1. [oceanbase.DBA_OB_TABLEGROUP_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051413)，展示 TABLEGROUP、TABLE 关联关系。不展示不在 TABLEGROUP 中的 TABLE。
8. 1. [oceanbase.DBA_PART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051504)，展示数据库中所有分区对象中分区键包含的列。
9. 1. [oceanbase.DBA_SUBPART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051397)，查看数据库中所有分区对象中子分区键包含的列。
10. 1. [oceanbase.CDB_OB_COLUMN_CHECKSUM_ERROR_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051660)，展示索引表（包括：全局索引和局部索引）与主表之间出现的列校验和不一致的信息。
11. 1. [oceanbase.CDB_OB_TABLE_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051614)，展示表或者分区所在的位置，包括：系统表、用户表、索引表等。
12. 1. [oceanbase.DBA_OB_TABLE_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051534)，展示表或者分区所在的位置，包括：系统表、用户表、索引表等。
13. information_schema.TABLESPACES，与表空间相关的信息；
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构。

###### 2.1.2 InnoDB 存储引擎相关
1. information_schema.INNODB_BUFFER_PAGE，展示缓冲池中所有缓存页的信息；
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
2. information_schema.INNODB_BUFFER_PAGE_LRU 展示 InnoDB 缓冲池中页面的 LRU 排序信息
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
3. information_schema.INNODB_BUFFER_POOL_STATS 展示 InnoDB 缓冲池统计信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
4. information_schema.INNODB_CMP展示与压缩 InnoDB 表相关的操作状态信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
5. information_schema.INNODB_CMP_PER_INDEX 展示与压缩 InnoDB 表和索引相关的操作的信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
6. information_schema.INNODB_CMP_PER_INDEX_RESET 展示与压缩的 InnoDB 表和索引相关操作重置时间信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
7. information_schema.INNODB_CMP_RESET 展示与压缩 InnoDB 表相关的操作状态。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
8. information_schema.INNODB_CMPMEM 展示 InnoDB 缓冲池中的压缩页面的状态。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
9. information_schema.INNODB_CMPMEM_RESET 展示 InnoDB 缓冲池中的压缩页面重置时间的状态。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
10. information_schema.INNODB_SYS_DATAFILES 展示每个表和通用表空间的 InnoDB 文件的数据文件路径信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
11. information_schema.INNODB_SYS_INDEXES 展示 InnoDB 索引元数据信息。 
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
12. information_schema.INNODB_SYS_TABLES 展示 InnoDB 表元数据信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
13. information_schema.INNODB_SYS_TABLESPACES 展示InnoDB 表文件和通用表空间的元数据。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
14. information_schema.INNODB_SYS_TABLESTATS 展示 InnoDB 表低级状态信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
15. information_schema.INNODB_SYS_VIRTUAL 展示 InnoDB 虚拟生成的列元数据。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
16. information_schema.INNODB_TEMP_TABLE_INFO 展示关于活动用户创建的 InnoDB 临时表的信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
17. information_schema.INNODB_METRICS 展示 InnoDB 性能信息。  
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；

###### 2.1.3 Outline
SYS 租户，MySql 租户，Oracle 租户 已合并；


###### 2.1.4 数据库基本信息
1. [oceanbase.CDB_OB_DATABASES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-；00000001051652) ：系统租户下展示所有租户的数据库元信息；
2. [oceanbase.CDB_OB_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051453) ：系统租户下展示所有租户的合并全局信息；
3. [oceanbase.CDB_OB_ZONE_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051554) ：系统租户下展示所有租户各个 Zone 的合并信息；
4. [oceanbase.DBA_OB_DATABASES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051493) ：展示租户的数据库元信息；
5. [oceanbase.DBA_OB_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051500) ：展示本租户的合并全局信息；
6. [oceanbase.DBA_OB_ROOTSERVICE_EVENT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051432) ：展示 Root Service 事件历史；
7. *DBA_OB_SERVER_JOBS*：
	1. [oceanbase.DBA_OB_SERVER_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051498) ：展示所有 OBServer 节点相关任务；
8. [oceanbase.DBA_OB_SERVERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051543) ：展示所有 OBServer 节点的信息；
9. [oceanbase.DBA_OB_ZONE_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051663) ：展示本租户各个 Zone 的合并信息；
10. [oceanbase.DBA_OB_ZONES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051408) ：展示所有 Zone 的信息；
11. [oceanbase.CDB_OB_FREEZE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051528) ：展示合并（major_freeze）产生的版本信息；
12. [oceanbase.DBA_OB_FREEZE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051590) ：展示合并（major_freeze）产生的版本信息；
13. [information_schema.events](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051479) ：用于记录所有 Event 信息；

###### 2.1.5 分区相关
1. [oceanbase.CDB_IND_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051592) ：展示所有租户的索引表的一级分区信息；
2. [oceanbase.CDB_PART_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051676) ：展示所有租户的索引表的分区信息；
3. [oceanbase.CDB_PART_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051461) ：展示所有租户的分区表的分区信息；
4. [oceanbase.CDB_SUBPARTITION_TEMPLATES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051426) ：展示所有租户的分区表的模板二级分区信息；
5. [oceanbase.CDB_TAB_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051602) ：展示所有租户的分区表的一级分区信息；
6. [oceanbase.CDB_TAB_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051539) ：展示所有租户的分区表的二级分区信息；
7. [oceanbase.DBA_IND_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051591) ：展示索引的一级/二级分区信息；
8. [oceanbase.DBA_PART_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051508) ：展示所有的分区索引的分区信息；
9. [oceanbase.DBA_PART_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051470) ：展示用户表分区信息；
10. [oceanbase.DBA_TAB_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051463) ：展示当前用户可访问的多级分区信息、分区存储参数和分区统计信息；
11. [oceanbase.DBA_TAB_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051611) ：展示子分区名称、表和所属分区的名称、其存储属性以及 `DBMS_STATS` 包生成的统计信息；
12. [oceanbase.DBA_SUBPARTITION_TEMPLATES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051489) ：展示所有子分区模版描述信息；

###### 2.1.6 负载均衡相关
1. [oceanbase.CDB_OB_TRANSFER_PARTITION_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051658) ：展示了所有租户当前正在处理的分区 Transfer 任务；
2. [oceanbase.CDB_OB_TRANSFER_PARTITION_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051467) ：展示了所有租户已执行的 Transfer Partition 任务历史；
3. [oceanbase.DBA_OB_TRANSFER_PARTITION_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051518) ：展示了本租户下当前正在处理的 Transfer Partition 任务；
4. [oceanbase.DBA_OB_TRANSFER_PARTITION_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051492) ：展示了本租户已执行的 Transfer Partition 任务历史；

###### 2.1.7 Session 相关
1. [oceanbase.CDB_OB_DEADLOCK_EVENT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051567) ：展示死锁事件的历史记录，该视图在 root 租户下展示所有租户的历史记录；
2. [oceanbase.DBA_OB_DEADLOCK_EVENT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051680) ：展示死锁事件的历史记录，该视图在非 root 租户下展示本租户的历史记录；
3. [oceanbase.DBA_OB_SERVER_EVENT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051622) ：展示 OBServer 节点事件历史信息；

###### 2.1.8 备份恢复相关
SYS 租户，MySql 租户，Oracle 租户 已合并；


###### 2.1.9 统计信息和直方图
1. [oceanbase.DBA_TAB_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051633) ：展示数据库中所有表的优化器统计信息；
2. [oceanbase.DBA_TAB_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051443) ：展示数据库中所有表的列统计信息和直方图信息；
3. [oceanbase.DBA_PART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051678) ：展示数据库中所有分区表的列统计信息和直方图信息；
4. [oceanbase.DBA_SUBPART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051444) ：展示数据库中所有子分区表的列统计信息和直方图信息；
5. [oceanbase.DBA_TAB_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051570) ：展示数据库中所有表的列的直方图；
6. [oceanbase.DBA_PART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051481) ：展示数据库中所有分区表上的直方图数据（每个直方图的端点）；
7. [oceanbase.DBA_SUBPART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051513) ：展示数据库中所有子分区表上的直方图数据（每个直方图的端点）；
8. [oceanbase.DBA_TAB_STATS_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051420) ：展示数据库中所有表的表统计历史记录；
9. [oceanbase.DBA_IND_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051501) ：展示数据库中所有索引的优化器统计信息；
10. [oceanbase.DBA_TAB_MODIFICATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051435) ：展示数据库中所有表的自上次在该表上收集统计信息以来的修改信息；
11. [oceanbase.CDB_TAB_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051659) ：系统租户下展示列级统计信息；


###### 2.1.10 租户相关
1. [oceanbase.DBA_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051510) ：展示数据库所有数据库对象，视图定义与 ALL_OBJECTS 一致；
2. [oceanbase.CDB_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051472) ：系统租户下展示所有租户的对象信息；
3. [oceanbase.DBA_OB_TENANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051606) ：
	1. 展示所有租户的基本信息。包括系统租户、用户创建的租户（用户租户），以及 Meta 租户。用户可以根据 `TENANT_TYPE` 列来区分租户类型；
4. *DBA_OB_TENANT_JOBS*：
	1. [oceanbase.DBA_OB_TENANT_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051505) ：展示租户级别的任务信息；
5. [oceanbase.CDB_OB_SYS_VARIABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051424) ：在系统租户下展示所有租户的系统变量信息；


###### 2.1.11 资源相关
1. [oceanbase.DBA_OB_RESOURCE_POOLS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051417) ：展示所有租户的资源池信息；
2. *DBA_OB_UNIT_JOBS*：
	1. [oceanbase.DBA_OB_UNIT_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051401) ：展示所有 UNIT 相关任务；
3. [oceanbase.DBA_OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051526) ：展示所有租户的 UNIT 信息；
4. [oceanbase.DBA_OB_UNIT_CONFIGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051421) ：展示所有租户的 UNIT 规格信息；
5. [oceanbase.DBA_OB_SEQUENCE_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051460) ：展示数据库中所有 SEQUENCE；


###### 2.1.12 副本和日志相关
1. [oceanbase.CDB_IND_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051575) ：查看数据库中所有索引子分区的信息；
2. [oceanbase.CDB_OB_LS_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051639) ：展示所有租户的日志流（ LS ）副本分布信息；
3. [oceanbase.CDB_OB_TABLEGROUPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051558) ：系统租户下展示所有租户的表组的元信息，主要是展示表组的分区信息；
4. [oceanbase.CDB_OB_TABLEGROUP_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051627) ：系统租户下展示所有租户的分区表组的一级分区信息；
5. [oceanbase.CDB_OB_TABLEGROUP_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051473) ：系统租户下显示所有租户的分区表组的二级分区信息；
6. [oceanbase.CDB_OB_TABLET_TO_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051488) ：系统租户下展示所有租户的 Tablet 列表以及 Tablet 所属的日志流信息；
7. [oceanbase.CDB_OB_TABLET_REPLICAS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051412) ：
	1. 展示本租户的所有 TABLET 副本信息，每个 TABLET 副本由联合键唯一确定：`<TENANT_ID, TABLET_ID, SVR_IP, SVR_PORT, LS_ID>`；
8. [oceanbase.CDB_PART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051594) ：展示所有租户的分区对象（分区表、分区索引）的一级分区键信息；
9. [oceanbase.CDB_SUBPART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051471) ：展示所有租户的分区对象（分区表、分区索引）的二级分区键信息；
10. [oceanbase.DBA_IND_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051607) ：展示数据库中所有索引子分区的信息；
11. [oceanbase.DBA_OB_TABLEGROUP_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051596) ：展示分区的表组的一级分区信息；
12. [oceanbase.DBA_OB_TABLEGROUP_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051536) ：展示分区的表组的二级分区信息；
13. [oceanbase.DBA_OB_TABLEGROUPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051520) ：展示表组的元信息，主要是展示表组的分区信息；
14. [oceanbase.DBA_OB_TABLET_REPLICAS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051657) ：
	1. 展示本租户的所有 TABLET 副本信息，每个 TABLET 副本由联合键唯一确定：`<TENANT_ID, TABLET_ID, SVR_IP, SVR_PORT, LS_ID>`；
15. [oceanbase.DBA_OB_TABLET_TO_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051574) ：展示本租户的所有 Tablet 列表以及 Tablet 所属的日志流信息；
16. [oceanbase.DBA_OB_LS_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051546) ：展示本租户的日志流（ LS ）副本分布信息；
17. [oceanbase.CDB_OB_TABLET_CHECKSUM_ERROR_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051478) ：展示 TABLET 副本之间出现的数据不一致的信息；
18. [oceanbase.CDB_OB_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051515) ：展示日志流的状态和恢复进度；
19. [oceanbase.DBA_OB_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051648) ：展示日志流的状态和恢复进度；

###### 2.1.13 WR 相关
1. [oceanbase.DBA_WR_SNAPSHOT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051549) ：展示本租户的 `SNAPSHOT` 信息；
2. [oceanbase.CDB_WR_SNAPSHOT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051399) ：展示所有租户的 `SNAPSHOT` 信息；
3. [oceanbase.DBA_WR_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051550) ：展示本租户持久化后的 ASH 数据；
4. [oceanbase.CDB_WR_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051569) ：展示所有租户持久化后的 ASH 数据；
5. [oceanbase.DBA_WR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051503) ：展示本租户的系统统计项的名称；
6. [oceanbase.CDB_WR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051442) ：展示所有租户的系统统计项的名称；
7. [oceanbase.DBA_WR_SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051597) ：展示本租户的系统统计项的详细信息；
8. [oceanbase.CDB_WR_SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051516) ：展示所有租户的系统统计项的详细信息；
9. [oceanbase.DBA_WR_CONTROL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051604) ：展示 WR 相关的配置信息 
10. [oceanbase.CDB_WR_CONTROL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051576) ：展示 WR 相关的配置信息；
11. [oceanbase.CDB_WR_SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051482) ：用于展示所有租户的持久化后的租户级等待事件信息；
12. [oceanbase.DBA_WR_SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051618) ：用于展示本租户的持久化后的租户级等待事件信息；
13. [oceanbase.CDB_WR_EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051560) ：用于展示所有租户的持久化后的租户级等待事件名称；
14. [oceanbase.DBA_WR_EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051431) ：用于展示本租户的持久化后的租户级等待事件名称；
15. [oceanbase.CDB_WR_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051469) ：
	1. 用于展示所有租户执行过的 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；
16. [oceanbase.DBA_WR_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051447) ：
	1. 用于展示本租户执行过的 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；
17. [oceanbase.CDB_WR_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051674) ：用于展示所有租户 Time Model 统计项的 WR 数据；
18. [oceanbase.DBA_WR_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051450) ：用于展示本租户 Time Model 统计项的 WR 数据；
19. [oceanbase.CDB_WR_SQLTEXT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051669) ：用于展示所有租户 WR 采集的 SQL 文本；
20. [oceanbase.DBA_WR_SQLTEXT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051522) ：用于展示本租户 WR 采集的 SQL 文本；

###### 2.1.14 OBKV 相关
1. [oceanbase.CDB_OB_KV_TTL_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051483) ：展示系统租户下查看到所有租户正在执行的 TTL 任务；
2. [oceanbase.CDB_OB_KV_TTL_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051552) ：展示系统租户下查看到所有租户的历史 TTL 任务；
3. [oceanbase.DBA_OB_KV_TTL_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051416) ：展示当前租户正在执行的 TTL 任务；
4. [oceanbase.DBA_OB_KV_TTL_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051405) ：展示当前租户的历史 TTL 任务；


##### 2.2 性能视图总览
###### 2.2.1 事件相关的统计信息
1. [GV$OB_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051745)：展示租户的 OBServer 级合并进度信息；
2. [GV$OB_COMPACTION_SUGGESTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051819)：展示 COMPACTION 的建议信息；
3. [GV$OB_PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051690)：展示租户所在的所有 OBServer 节点的会话信息；
4. [GV$OB_COMPACTION_DIAGNOSE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051722)：展示 COMPACTION 的诊断信息；
5. [GV$OB_TABLET_COMPACTION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051730)：展示 TABLET 级 COMPACTION 的历史信息；
6. [GV$OB_TABLET_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051794)：展示 TABLET 级 COMPACTION 的进度信息；
7. [GV$OB_TRANSACTION_PARTICIPANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051705)：展示所有 OBServer 节点上的事务参与者信息；
8. [GV$SESSION_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051708)：用于展示集群内所有 OBServer 节点的 Session 级别等待事件；
9. [GV$SESSION_WAIT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051791)：该视图用于展示所有服务器上所有 Session 的当前或者上一次等待事件的一些信息，包括等待事件名称、等待耗时等；
10. [GV$SESSION_WAIT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051769)：用于展示所有服务器上所有 Session 的最近 10 次等待事件的信息；
11. [GV$SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051744)：展示集群所有租户级别的等待事件；
12. [GV$SESSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051692)：所有 OBServer 节点的 Session 级别的统计事件信息；
13. [GV$SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051785)：展示所有服务器上租户级别的统计事件信息；
14. [GV$ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051813)：展示活跃会话历史记录；
15. [V$ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051713)：展示活跃会话历史记录；
16. [V$OB_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051774)：展示租户的 Server 级合并进度信息；
17. [V$OB_COMPACTION_SUGGESTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051706)：展示 COMPACTION 的建议信息；
18. [V$OB_PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051752)：展示本 OBServer 节点的会话信息；
19. [V$OB_COMPACTION_DIAGNOSE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051707)：展示 COMPACTION 的诊断信息；
20. [V$OB_TABLET_COMPACTION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051737)：展示 TABLET 级 COMPACTION 的历史信息；
21. [V$OB_TABLET_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051772)：展示 TABLET 级 COMPACTION 的进度信息；
22. [V$OB_TRANSACTION_PARTICIPANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051800)：展示所有 OBServer 节点上的事务参与者信息；
23. [V$STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051743)：用于展示所有统计事件的相关定义；
24. [V$EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051732)：当前 OBServer 节点上所有统计事件的相关定义；
25. [V$SESSION_WAIT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051694)：每个 Session 当前等待事件明细，按照约定，每个等待事件会有三个参数，各自会记录对应的值；
26. [V$SESSION_WAIT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051801)：每个 Session 当前等待事件明细，最近 10 次等待事件；
27. [V$SESSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051802)：展示 Session 级别的统计事件概况；
28. [V$SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051799)：展示当前服务器上租户级别的统计事件信息；
29. [V$SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051749)：租户级别的等待事件统计；
30. [GV$SESSION_LONGOPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051755)：展示集群索引构建的进度；
31. [V$SESSION_LONGOPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051704)：展示当前 OBServer 节点上索引构建的进度；

###### 2.2.2 监控相关的统计信息
1. [GV$OB_DTL_INTERM_RESULT_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051741)：展示 DTL 中间结果管理器的监控信息；
2. [V$OB_DTL_INTERM_RESULT_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051762)：展示 DTL 中间结果管理器的监控信息；

###### 2.2.3 I/O 相关的统计信息
1. [GV$OB_IO_CALIBRATION_STATUS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051726)：展示 I/O 校准任务的执行状态；
2. [V$OB_IO_CALIBRATION_STATUS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051763)：展示 I/O 校准任务的执行状态；
3. [GV$OB_IO_BENCHMARK](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051723)：展示 I/O 模块使用的磁盘校准数据；
4. [V$OB_IO_BENCHMARK](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051750)：展示 I/O 模块使用的磁盘校准数据；

###### 2.2.4 缓存相关的统计信息
1. [GV$OB_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051778)：用于展示租户级别的内存统计信息；
2. [V$OB_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051767)：用于展示租户级别的内存统计信息；
3. [GV$OB_MEMSTORE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051700)：用于展示所有服务器上所有租户的 MemTable 的内存使用状况；
4. [V$OB_MEMSTORE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051781)：用于展示租户级别的 MemStore 统计信息；
5. [GV$OB_MEMSTORE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051736)：展示所有服务器上所有租户的所有分区的 MemTable 的详细信息；
6. [V$OB_MEMSTORE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051740)：展示所有服务器上所有租户的所有分区的 MemTable 的详细信息；
7. [GV$OB_SSTABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051695)：展示租户的 SSTable 信息；
8. [V$OB_SSTABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051748)：展示租户的 SSTable 信息；
9. [GV$OB_TENANT_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051738)：用于展示集群上每个租户的内存使用状况；
10. [V$OB_TENANT_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051792)：用于展示当前 observer 进程中每个租户的内存使用状况；

###### 2.2.5 SQL 相关的统计信息_已迁移至



7. [GV$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051811)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 item 数目；
8. [V$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051765)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 item 数目；
9. [GV$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051807)：用于给 prepared statement 提供监控功能，包含 PS Cache 中所有 prepare statement 的基本信息；
10. [V$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051739)：用于给 prepared statement 提供监控功能，包含 PS Cache 中所有 prepare statement 的基本信息；
11. [GV$OB_PX_P2P_DATAHUB](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051779)：展示并行执行的数据传输操作的相关信息；
12. [GV$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051714)：展示所有 OBServer 节点上的 PX 线程使用情况；
13. [V$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051746)：展示 OBServer 节点上的 PX 线程使用情况；
15. [GV$SQL_PLAN_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051689)：展示所有 OBServer 节点慢查询的 Plan 层面的统计，每个慢查询都会有一条统计信息，同时记录该 Plan 的 Trace 信息；
16. [V$SQL_PLAN_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051757)：慢查询的 Operation 层面的统计，每个 Operation 会有一条统计信息；
17. [V$SQL_MONITOR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051812)：展示 `GV$SQL_PLAN_MONITOR` 的 `OTHERSTAT_X_ID`字段中数字的含义；
18. [GV$OB_FLT_TRACE_CONFIG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051718)：展示全链路诊断的诊断信息；
19. [GV$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051776)：用于展示集群中 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；
20. [V$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051775)：用于展示当前节点上 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；

###### 2.2.6 日志相关的统计信息
1. [GV$OB_LOG_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051806)：展示 Palf 的状态；
2. [GV$OB_LS_SNAPSHOTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051751)：展示所有 Unit 中持久化存储的日志流快照的信息；
3. [V$OB_LOG_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051804)：展示 Palf 的状态；

###### 2.2.7 网络相关的统计信息
1. [GV$OB_RPC_OUTGOING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051783)：所有 OBServer 节点的 RPC 的发送统计信息；
2. [GV$OB_RPC_INCOMING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051758)：用于展示所有 OBServer 节点收到 RPC 请求的统计信息；
3. [V$OB_RPC_OUTGOING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051803)：统计 OBServer 节点上不同租户所有不同 RPC Packet Code 的 RPC 数据发送情况；
4. [V$OB_RPC_INCOMING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051724)：用于统计 OBServer 节点上不同租户所有的不同 RPC Packet Code 的 RPC 数据接收情况；

###### 2.2.8 Session 相关
1. [GV$OB_SESSION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051808)：记录所有 Server 中创建的 Session 信息；

###### 2.2.9 数据库相关的统计信息
1. [[15_OceanBase/99_内部表介绍/(G)V$OB_PARAMETERS\|(G)V$OB_PARAMETERS]]，；
	1. 
2. [GV$OB_KVCACHE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051760)：展示每台 OBServer 节点上的 KVCACHE 信息；
3.
4. [GV$OB_SERVER_SCHEMA_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051683)：展示所有 Server 上的 Schema 信息；
5. [GV$OB_SERVERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051773)：展示所有 OBServer 节点的信息；
6. [GV$OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051768)：展示租户所在的 OBServer 节点的 Unit 信息，系统租户看到本集群所有 OBServer 节点的 Unit 信息；
7. [V$OB_KVCACHE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051764)：展示每台 OBServer 节点上的 KVCACHE 信息；
8. 
9. [V$OB_SERVER_SCHEMA_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051761)：展示所有 Server 上的 Schema 信息；
10. [V$OB_SERVERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051711)：展示本 OBServer 节点的信息；
11. [V$OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051786)：展示本 OBServer 节点的 Unit 信息；
12. [GV$DML_STATS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051691)：展示当前用户表中的 DML 信息；
13. [V$DML_STATS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051780)：展示当前用户表中的 DML 信息；
14. [V$OB_COMPATIBILITY_CONTROL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051787)：用于展示所有可以按 OceanBase 数据库发行版本进行产品行为兼容控制的功能；

###### 2.2.10 Time Model 相关的统计信息
1. [GV$OB_SESS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051735)：用于展示集群中 Session 级别的 Time Model 统计项；
2. [V$OB_SESS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051766)：用于展示当前节点上 Session 级别的 Time Model 统计项；
3. [GV$OB_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051805)：用于展示集群中租户级别的 Time Model 统计项；
4. [V$OB_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051710)：用于展示当前节点上租户级别的 Time Model 统计项；

###### 2.2.11 转储相关的统计信息
1. [GV$OB_MERGE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051731)：展示 OceanBase 集群已经完成的 compaction 相关的统计信息；
2. [V$OB_MERGE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051820)：展示 OceanBase 集群已经完成的 compaction 相关的统计信息；

###### 2.2.12 线程相关的统计信息
1. [GV$OB_PX_WORKER_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051733)：在并行执行场景中，查看每个线程的执行信息；
2. [V$OB_PX_WORKER_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051798)：在并行执行场景中，查看每个线程的执行信息；
3. [GV$OB_TENANT_RUNTIME_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051684)：展示租户线程中队列和线程限额相关的信息；
4. [V$OB_TENANT_RUNTIME_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051770)：展示租户线程中队列和线程限额相关的信息；

###### 2.2.13 Workarea 相关的统计信息
1. [GV$SQL_WORKAREA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051682)：之前受 SQL 自动内存管理负责执行的所有 Operator 的 Workarea 统计信息；
2. [GV$SQL_WORKAREA_ACTIVE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051698)：展示当前活跃 Operator 的 Workarea 信息；
3. [GV$SQL_WORKAREA_HISTOGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051793)：展示之前所有 Workarea执行的统计信息，如在区间 1 M～2 M 之间全 in-memory 处理的次数、one-pass 次数等；
4. [GV$OB_SQL_WORKAREA_MEMORY_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051815)：用于查询 SQL Workarea 总体的一些信息，如最大可用内存、当前使用内存、当前 hold 内存等；
5. [V$SQL_WORKAREA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051777)：之前受 SQL 自动内存管理负责执行的所有 Operator 的 Workarea 统计信息；
6. [V$SQL_WORKAREA_ACTIVE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051747)：展示当前活跃 Operator 的 Workarea 信息；
7. [V$SQL_WORKAREA_HISTOGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051784)：展示之前所有 workarea 执行的统计信息，如在区间1M～2M之间全 in-memory 处理的次数、one-pass 次数等；
8. [V$OB_SQL_WORKAREA_MEMORY_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051756)：用于查询 SQL workarea 总体的一些信息，包括最大可用内存、当前使用内存、当前 hold 内存等；

###### 2.2.14 加密表相关的统计信息
1. [V$OB_ENCRYPTED_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051810)：用于展示开启加密功能的表的加密状态；
2. [V$ENCRYPTED_TABLESPACES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051754)：用于展示开启加密功能的表空间的加密状态；

###### 2.2.15 事务相关的统计信息
1. [GV$LATCH](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051789)：集群所有 OBServer 节点的 Latch 信息视图；
2. [V$LATCH](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051687)：集群中所有 OBServer 节点的 Latch 信息视图；

###### 2.2.16 ASH 相关的统计信息
1. [GV$OB_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051725)：用于展示活跃会话历史记录；
2. [V$OB_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051788)：用于展示当前活跃会话历史记录；


#### 3 MySQL 租户下视图总览

##### 3.1 字典视图总览
###### 3.1.1 Help 命令
1. [mysql.help_topic](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052012)：与其他三个表 `mysql.help_category`、`mysql.help_relation`、`mysql.help_keyword` 合作生成 Help 命令；
2. [mysql.help_category](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052064)：与其他三个表 `mysql.help_relation`、`mysql.help_topic`、`mysql.help_keyword` 合作生成 Help 命令；
3. [mysql.help_keyword](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052068)：与其他三个表 `mysql.help_relation`、`mysql.help_topic`、`mysql.help_category` 合作生成 Help 命令；
4. [mysql.help_relation](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052058)：与其他三个表 `mysql.help_category`、`mysql.help_topic`、`mysql.help_keyword` 合作生成 Help 命令；

###### 3.1.2 SPM 相关
1. [oceanbase.DBA_SQL_PLAN_BASELINES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052054)：展示 SPM 中 SQL 的计划基线；
2. [oceanbase.DBA_SQL_MANAGEMENT_CONFIG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051987)：展示 SPM 中的配置参数；

###### 3.1.3 权限相关
1. [mysql.db](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052019)：用于展示数据库级别的权限信息（VIRTUAL_TABLE）；
2. [mysql.user](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052031)：用于记录用户级别的权限信息（VIRTUAL_TABLE）；
3. [information_schema.COLUMN_PRIVILEGES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051996)：展示列权限信息；
4. [information_schema.SCHEMA_PRIVILEGES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052117)：描述数据库权限信息；
5. [information_schema.TABLE_PRIVILEGES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051981)：记录表权限信息；
6. [information_schema.USER_PRIVILEGES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052073)：记录用户权限信息；


###### 3.1.4 OBKV 相关
1. [oceanbase.DBA_OB_KV_TTL_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051959)：展示当前租户正在执行的 TTL 任务；
2. [oceanbase.DBA_OB_KV_TTL_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052087)：展示当前租户的历史 TTL 任务；

###### 3.1.5 调度程序
1. [oceanbase.DBA_SCHEDULER_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051995)：展示数据库中所有调度程序作业的信息；


###### 3.1.6 存储相关
1. [mysql.proc](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052030)：用于展示存储过程的信息；
2. [information_schema.ENGINES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051964)：记录存储引擎相关信息；
3. [information_schema.PARAMETERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052116)：提供存储过程参数和返回值相关信息；
4. [information_schema.ROUTINES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052029)：提供存储过程相关的信息；


###### 3.1.7 时区相关
1. [mysql.time_zone](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052077)：用于展示时区相关的信息（SYSTEM_VIEW）；
2. [mysql.time_zone_name](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052092)：用于记录 Timezone 的名称信息（SYSTEM_VIEW）；
3. [mysql.time_zone_transition](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051993)：`mysql.time_zone_transition` 是用于记录时区转换信息的系统视图；
4. [mysql.time_zone_transition_type](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052074)：用于记录时区转换类型（SYSTEM_VIEW）；


###### 3.1.8 字符集
1. [information_schema.CHARACTER_SETS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052022)：记录 OceanBase 数据库的字符集信息；
2. [information_schema.COLLATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051988)：记录每个字符集的比对方法信息；
3. [information_schema.COLLATION_CHARACTER_SET_APPLICABILITY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052096)：记录 Character 和 Collation 之间的对应关系；


###### 3.1.9 表和列相关
1. [information_schema.COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052097)：记录列信息；
2. [information_schema.STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052053)：记录表索引信息；
3. [information_schema.TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052051)：记录表的信息；
4. [information_schema.VIEWS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051985)：记录视图信息；
5. [information_schema.VIEW_TABLE_USAGE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052020)：展示视图定义中使用的表和视图的信息；
6. [oceanbase.DBA_OB_TABLEGROUP_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052041)：展示 TABLEGROUP、TABLE 关联关系。不展示不在 TABLEGROUP 中的 TABLE；
7. [oceanbase.DBA_PART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052044)：展示数据库中所有分区对象中分区键包含的列；
8. [oceanbase.DBA_SUBPART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052005)：查看数据库中所有分区对象中子分区键包含的列；
9. [oceanbase.DBA_OB_TABLE_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052062)：展示表或者分区所在的位置，包括：系统表、用户表、索引表等；
10. information_schema.TABLESPACES：与表空间相关的信息；
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；


###### 3.1.10 InnoDB 存储引擎相关
1. information_schema.INNODB_BUFFER_PAGE1. 展示缓冲池中所有缓存页的信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
2. information_schema.INNODB_BUFFER_PAGE_LRU1. 展示 InnoDB 缓冲池中页面的 LRU 排序信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
3. information_schema.INNODB_BUFFER_POOL_STATS1. 展示 InnoDB 缓冲池统计信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
4. information_schema.INNODB_CMP1. 展示与压缩 InnoDB 表相关的操作状态信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
5. information_schema.INNODB_CMP_PER_INDEX1. 展示与压缩 InnoDB 表和索引相关的操作的信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
6. information_schema.INNODB_CMP_PER_INDEX_RESET1. 展示与压缩的 InnoDB 表和索引相关操作重置时间信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
7. information_schema.INNODB_CMP_RESET1. 展示与压缩 InnoDB 表相关的操作状态。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
8. information_schema.INNODB_CMPMEM1. 展示 InnoDB 缓冲池中的压缩页面的状态。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
9. information_schema.INNODB_CMPMEM_RESET1. 展示 InnoDB 缓冲池中的压缩页面重置时间的状态。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
10. information_schema.INNODB_SYS_DATAFILES1. 展示每个表和通用表空间的 InnoDB 文件的数据文件路径信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
11. information_schema.INNODB_SYS_INDEXES1. 展示 InnoDB 索引元数据信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
12. information_schema.INNODB_SYS_TABLES1. 展示 InnoDB 表元数据信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
13. information_schema.INNODB_SYS_TABLESPACES1. 展示InnoDB 表文件和通用表空间的元数据。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
14. information_schema.INNODB_SYS_TABLESTATS1. 展示 InnoDB 表低级状态信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
15. information_schema.INNODB_SYS_VIRTUAL1. 展示 InnoDB 虚拟生成的列元数据。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
16. information_schema.INNODB_TEMP_TABLE_INFO1. 展示关于活动用户创建的 InnoDB 临时表的信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；
17. information_schema.INNODB_METRICS1. 展示 InnoDB 性能信息。
	1. 说明：该视图仅用于兼容 MySQL 5.7，相关功能并不支持，仅可以查询表结构；


###### 3.1.11 Outline
SYS 租户，MySql 租户，Oracle 租户 已合并；



###### 3.1.12 数据库基本信息
1. [information_schema.GLOBAL_STATUS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052106)：记录 OceanBase 数据库的全局状态信息；
2. [information_schema.GLOBAL_VARIABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052071)：记录全局变量信息；
3. [information_schema.SCHEMATA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052102)：记录数据库元信息；
4. [oceanbase.DBA_OB_DATABASES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051983)：展示租户的数据库元信息；
5. [oceanbase.DBA_OB_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052123)：展示本租户的合并全局信息；
6. [oceanbase.DBA_OB_ZONE_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052124)：展示本租户各个 Zone 的合并信息；
7. [oceanbase.DBA_OB_FREEZE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051994)：展示合并（major_freeze）产生的版本信息；
8. [information_schema.events](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052014)：用于记录所有 Event 信息；


###### 3.1.13 分区相关
1. [information_schema.PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052119)：记录 Partition 信息；
2. [oceanbase.DBA_IND_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052094)：展示索引的一级/二级分区信息；
3. [oceanbase.DBA_PART_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052105)：展示所有的分区索引的分区信息；
4. [oceanbase.DBA_PART_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051972)：展示用户表分区信息；
5. [oceanbase.DBA_TAB_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052038)：展示当前用户可访问的多级分区信息、分区存储参数和分区统计信息；
6. [oceanbase.DBA_TAB_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052110)：展示子分区名称、表和所属分区的名称、其存储属性以及 `DBMS_STATS` 包生成的统计信息；
7. [oceanbase.DBA_SUBPARTITION_TEMPLATES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052037)：展示所有子分区模版描述信息；


###### 3.1.14 负载均衡相关
1. [oceanbase.DBA_OB_TRANSFER_PARTITION_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052021)：展示了本租户下当前正在处理的 Transfer Partition 任务；
2. [oceanbase.DBA_OB_TRANSFER_PARTITION_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051968)：展示了本租户已执行的 Transfer Partition 任务历史；


###### 3.1.15 线程相关
1. [information_schema.PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051966)：提供运行中的线程信息；


###### 3.1.16 约束
1. [information_schema.CHECK_CONSTRAINTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051965)：展示 `CREATE TABLE` 操作时，有关表和列相关的约束信息；
2. [information_schema.KEY_COLUMN_USAGE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052004)：记录 Column 中 Key 的约束信息；
3. [information_schema.REFERENTIAL_CONSTRAINTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051962)：用于展示外键约束的元数据；
4. [information_schema.TABLE_CONSTRAINTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052091)：描述表约束信息；


###### 3.1.17 Session 相关
1. [information_schema.SESSION_STATUS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052017)：记录 Session 的状态信息，如当前活跃连接数，数据库活跃时间（Uptime）等；
2. [information_schema.SESSION_VARIABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051963)：记录 Session 的变量信息；
3. [oceanbase.DBA_OB_DEADLOCK_EVENT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052109)：展示死锁事件的历史记录，该视图在非 root 租户下展示本租户的历史记录；


###### 3.1.18 用户登录相关
1. [information_schema.CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051986)：用于展示不同用户登录失败的尝试次数；


###### 3.1.19 备份恢复相关
SYS 租户，MySql 租户，Oracle 租户 已合并；



###### 3.1.20 统计信息和直方图
1. [oceanbase.DBA_TAB_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052034)：展示数据库中所有表的优化器统计信息；
2. [oceanbase.DBA_TAB_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051979)：展示数据库中所有表的列统计信息和直方图信息；
3. [oceanbase.DBA_PART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051997)：展示数据库中所有分区表的列统计信息和直方图信息；
4. [oceanbase.DBA_SUBPART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051973)：展示数据库中所有子分区表的列统计信息和直方图信息；
5. [oceanbase.DBA_TAB_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052040)：展示数据库中所有表的列的直方图；
6. [oceanbase.DBA_PART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052098)：展示数据库中所有分区表上的直方图数据（每个直方图的端点）；
7. [oceanbase.DBA_SUBPART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052111)：展示数据库中所有子分区表上的直方图数据（每个直方图的端点）；
8. [oceanbase.DBA_TAB_STATS_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052024)：展示数据库中所有表的表统计历史记录；
9. [oceanbase.DBA_IND_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052047)：展示数据库中所有索引的优化器统计信息；
10. [oceanbase.DBA_TAB_MODIFICATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052032)：展示数据库中所有表的自上次在该表上收集统计信息以来的修改信息；
11. [information_schema.QUERY_RESPONSE_TIME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052027)：展示 RESPONSE TIME 直方图等数据类型（Histogram）供 Prometheus 进行监控；


###### 3.1.21 租户相关
1. [oceanbase.DBA_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051984)：展示数据库所有数据库对象，视图定义与 ALL_OBJECTS 一致；
2. [oceanbase.DBA_OB_TENANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051975)：展示所有租户的基本信息。包括系统租户、用户创建的租户（用户租户），以及 Meta 租户。用户可以根据 `TENANT_TYPE` 列来区分租户类型；

###### 3.1.22 资源相关
1. [oceanbase.DBA_OB_SEQUENCE_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052103)：展示数据库中所有 SEQUENCE；

###### 3.1.23 副本和日志相关
1. [oceanbase.DBA_IND_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052013)：展示数据库中所有索引子分区的信息；
2. [oceanbase.DBA_OB_TABLEGROUP_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052045)：展示分区的表组的一级分区信息；
3. [oceanbase.DBA_OB_TABLEGROUP_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052011)：展示分区的表组的二级分区信息；
4. [oceanbase.DBA_OB_TABLEGROUPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052063)：展示表组的元信息，主要是展示表组的分区信息；
5. [oceanbase.DBA_OB_TABLET_REPLICAS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052048)：展示本租户的所有 TABLET 副本信息，每个 TABLET 副本由联合键唯一确定：`<TENANT_ID, TABLET_ID, SVR_IP, SVR_PORT, LS_ID>`；
6. [oceanbase.DBA_OB_TABLET_TO_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052100)：展示本租户的所有 Tablet 列表以及 Tablet 所属的日志流信息；
7. [oceanbase.DBA_OB_LS_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052061)：展示本租户的日志流（ LS ）副本分布信息；


###### 3.1.24 WR 相关
1. [oceanbase.DBA_WR_SNAPSHOT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052121)：展示本租户的 `SNAPSHOT` 信息；
2. [oceanbase.DBA_WR_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052126)：展示本租户持久化后的 ASH 数据；
3. [oceanbase.DBA_WR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052026)：展示本租户的系统统计项的名称；
4. [oceanbase.DBA_WR_SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052076)：展示本租户的系统统计项的详细信息；
5. [oceanbase.DBA_WR_CONTROL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051967)：展示 WR 相关的配置信息1. 
6. [oceanbase.DBA_WR_SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052114)：用于展示本租户的持久化后的租户级等待事件信息；
7. [oceanbase.DBA_WR_EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051998)：用于展示本租户的持久化后的租户级等待事件名称；
8. [oceanbase.DBA_WR_SQLTEXT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052083)：用于展示本租户 WR 采集的 SQL 文本；

###### 3.1.25 SQLSTAT 相关
1. [oceanbase.DBA_WR_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052118)：用于展示本租户执行过的 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；

###### 3.1.26 Time Model 相关
1. [oceanbase.DBA_WR_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052043)：展示了本租户 Time Model 统计项的 WR 数据；

##### 3.2 性能视图总览
###### 3.2.1 ASH 相关的统计信息
1. [GV$OB_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051904)：用于展示活跃会话历史记录；
2. [V$OB_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051946)：用于展示当前活跃会话历史记录；

###### 3.2.2 事件相关的统计信息
1. [GV$OB_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051894)：展示租户的 OBServer 级合并进度信息；
2. [GV$OB_COMPACTION_SUGGESTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051881)：展示 COMPACTION 的建议信息；
3. [GV$OB_PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051951)：展示租户所在的所有 OBServer 节点的会话信息；
4. [GV$OB_COMPACTION_DIAGNOSE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051878)：展示 COMPACTION 的诊断信息；
5. [GV$OB_TABLET_COMPACTION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051876)：展示 TABLET 级 COMPACTION 的历史信息；
6. [GV$OB_TABLET_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051933)：展示 TABLET 级 COMPACTION 的进度信息；
7. [GV$OB_TRANSACTION_PARTICIPANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051836)：展示所有 OBServer 节点上的事务参与者信息；
8. [GV$SESSION_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051906)：用于展示集群内所有 OBServer 节点的 Session 级别等待事件；
9. [GV$SESSION_WAIT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051916)：该视图用于展示所有服务器上所有 Session 的当前或者上一次等待事件的一些信息，包括等待事件名称、等待耗时等；
10. [GV$SESSION_WAIT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051908)：用于展示所有服务器上所有 Session 的最近 10 次等待事件的信息；
11. [GV$SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051838)：展示集群所有租户级别的等待事件；
12. [GV$SESSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051929)：所有 OBServer 节点的 Session 级别的统计事件信息；
13. [GV$SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051830)：展示所有服务器上租户级别的统计事件信息；
14. [GV$ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051915)：展示活跃会话历史记录；
15. [V$ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051928)：展示活跃会话历史记录；
16. [V$OB_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051821)：展示租户的 Server 级合并进度信息；
17. [V$OB_COMPACTION_SUGGESTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051833)：展示 COMPACTION 的建议信息；
18. [V$OB_PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051932)：展示本 OBServer 节点的会话信息；
19. [V$OB_COMPACTION_DIAGNOSE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051927)：展示 COMPACTION 的诊断信息；
20. [V$OB_TABLET_COMPACTION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051950)：展示 TABLET 级 COMPACTION 的历史信息；
21. [V$OB_TABLET_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051922)：展示 TABLET 级 COMPACTION 的进度信息；
22. [V$OB_TRANSACTION_PARTICIPANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051934)：展示所有 OBServer 节点上的事务参与者信息；
23. [V$STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051886)：用于展示所有统计事件的相关定义；
24. [V$EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051948)：当前 OBServer 节点上所有统计事件的相关定义；
25. [V$SESSION_WAIT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051882)：每个 Session 当前等待事件明细，按照约定，每个等待事件会有三个参数，各自会记录对应的值；
26. [V$SESSION_WAIT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051956)：每个 Session 当前等待事件明细，最近 10 次等待事件；
27. [V$SESSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051958)：展示 Session 级别的统计事件概况；
28. [V$SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051877)：展示当前服务器上租户级别的统计事件信息；
29. [V$SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051884)：租户级别的等待事件统计；
30. [GV$SESSION_LONGOPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051829)：展示集群索引构建的进度；
31. [V$SESSION_LONGOPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051911)：展示当前 OBServer 节点上索引构建的进度；

###### 3.2.3 监控相关的统计信息
1. [GV$OB_DTL_INTERM_RESULT_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051942)：展示 DTL 中间结果管理器的监控信息；
2. [V$OB_DTL_INTERM_RESULT_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051869)：展示 DTL 中间结果管理器的监控信息；

###### 3.2.4 I/O 相关的统计信息
1. [GV$OB_IO_CALIBRATION_STATUS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051831)：展示 I/O 校准任务的执行状态；
2. [V$OB_IO_CALIBRATION_STATUS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051923)：展示 I/O 校准任务的执行状态；
3. [GV$OB_IO_BENCHMARK](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051926)：展示 I/O 模块使用的磁盘校准数据；
4. [V$OB_IO_BENCHMARK](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051937)：展示 I/O 模块使用的磁盘校准数据；

###### 3.2.5 缓存相关的统计信息
1. [GV$OB_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051944)：用于展示租户级别的内存统计信息；
2. [V$OB_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051910)：用于展示租户级别的内存统计信息；
3. [GV$OB_MEMSTORE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051914)：用于展示所有服务器上所有租户的 MemTable 的内存使用状况；
4. [V$OB_MEMSTORE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051898)：用于展示租户级别的 MemStore 统计信息；
5. [GV$OB_MEMSTORE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051872)：展示所有服务器上所有租户的所有分区的 MemTable 的详细信息；
6. [V$OB_MEMSTORE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051854)：展示所有服务器上所有租户的所有分区的 MemTable 的详细信息；
7. [GV$OB_SSTABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051861)：展示租户的 SSTable 信息；
8. [V$OB_SSTABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051853)：展示租户的 SSTable 信息；
9. [GV$OB_TENANT_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051921)：用于展示集群上每个租户的内存使用状况；
10. [V$OB_TENANT_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051901)：用于展示当前 observer 进程中每个租户的内存使用状况；

###### 3.2.6 SQL 相关的统计信息



7. [GV$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051919)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 item 数目；
8. [V$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051868)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 item 数目；
9. [GV$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051828)：用于给 prepared statement 提供监控功能，包含 PS Cache 中所有 prepare statement 的基本信息；
10. [V$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051843)：用于给 prepared statement 提供监控功能，包含 PS Cache 中所有 prepare statement 的基本信息；
11. [GV$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051889)：展示所有 OBServer 节点上的 PX 线程使用情况；
12. [V$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051852)：展示 OBServer 节点上的 PX 线程使用情况；

15. [GV$SQL_PLAN_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051955)：展示所有 OBServer 节点慢查询的 Plan 层面的统计，每个慢查询都会有一条统计信息，同时记录该 Plan 的 Trace 信息；
16. [V$SQL_PLAN_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051936)：慢查询的 Operation 层面的统计，每个 Operation 会有一条统计信息；
17. [V$SQL_MONITOR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051871)：展示 `GV$SQL_PLAN_MONITOR` 的 `OTHERSTAT_X_ID`字段中数字的含义；
18. [GV$OB_FLT_TRACE_CONFIG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051885)：展示全链路诊断的诊断信息；

###### 3.2.7 日志相关的统计信息
1. [GV$OB_LOG_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051840)：展示 Palf 的状态；
2. [V$OB_LOG_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051874)：展示 Palf 的状态；

###### 3.2.8 网络相关的统计信息
1. [GV$OB_RPC_OUTGOING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051920)：所有 OBServer 节点的 RPC 的发送统计信息；
2. [GV$OB_RPC_INCOMING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051866)：用于展示所有 OBServer 节点收到 RPC 请求的统计信息；
3. [V$OB_RPC_OUTGOING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051841)：统计 OBServer 节点上不同租户所有不同 RPC Packet Code 的 RPC 数据发送情况；
4. [V$OB_RPC_INCOMING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051912)：用于统计 OBServer 节点上不同租户所有的不同 RPC Packet Code 的 RPC 数据接收情况；

###### 3.2.9 数据库相关的统计信息
1. [GV$OB_KVCACHE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051844)：展示每台 OBServer 节点上的 KVCACHE 信息；

4. [GV$OB_SERVER_SCHEMA_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051822)：展示所有 Server 上的 Schema 信息；
5. [GV$OB_SERVERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051924)：展示所有 OBServer 节点的信息；
6. [GV$OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051957)：展示租户所在的 OBServer 节点的 Unit 信息，系统租户看到本集群所有 OBServer 节点的 Unit 信息；
7. [V$OB_KVCACHE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051945)：展示每台 OBServer 节点上的 KVCACHE 信息；
7.
8. [V$OB_SERVER_SCHEMA_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051913)：展示所有 Server 上的 Schema 信息；
9. [V$OB_SERVERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051900)：展示本 OBServer 节点的信息；
10. [V$OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051835)：展示本 OBServer 节点的 Unit 信息；
11. [GV$DML_STATS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051845)：展示当前用户表中的 DML 信息；
12. [V$DML_STATS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051917)：展示当前用户表中的 DML 信息；
13. [V$OB_COMPATIBILITY_CONTROL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051899)：用于展示所有可以按 OceanBase 数据库发行版本进行产品行为兼容控制的功能；

###### 3.2.10 SQLSTAT 相关的统计信息
1. [GV$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051826)：用于展示集群中 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；
2. [V$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051941)：用于展示当前节点上 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；

###### 3.2.11 Time Model 相关统计信息
1. [GV$OB_SESS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051925)：用于展示集群中 Session 级别的 Time Model 统计项；
2. [V$OB_SESS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051879)：用于展示当前节点上 Session 级别的 Time Model 统计项；
3. [GV$OB_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051949)：用于展示集群中租户级别的 Time Model 统计项；
4. [V$OB_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051891)：用于展示当前节点上租户级别的 Time Model 统计项；

###### 3.2.12 转储相关的统计信息
1. [GV$OB_MERGE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051895)：展示 OceanBase 集群已经完成的 compaction 相关的统计信息；
2. [V$OB_MERGE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051953)：展示 OceanBase 集群已经完成的 compaction 相关的统计信息；

###### 3.2.13 线程相关的统计信息
1. [GV$OB_PX_WORKER_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051856)：在并行执行场景中，查看每个线程的执行信息；
2. [V$OB_PX_WORKER_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051842)：在并行执行场景中，查看每个线程的执行信息；

###### 3.2.14 Workarea 相关的统计信息
1. [GV$SQL_WORKAREA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051888)：之前受 SQL 自动内存管理负责执行的所有 Operator 的 Workarea 统计信息；
2. [GV$SQL_WORKAREA_ACTIVE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051931)：展示当前活跃 Operator 的 Workarea 信息；
3. [GV$SQL_WORKAREA_HISTOGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051848)：展示之前所有 Workarea执行的统计信息，如在区间 1 M～2 M 之间全 in-memory 处理的次数、one-pass 次数等；
4. [GV$OB_SQL_WORKAREA_MEMORY_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051849)：用于查询 SQL Workarea 总体的一些信息，如最大可用内存、当前使用内存、当前 hold 内存等；
5. [V$SQL_WORKAREA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051863)：之前受 SQL 自动内存管理负责执行的所有 Operator 的 Workarea 统计信息；
6. [V$SQL_WORKAREA_ACTIVE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051939)：展示当前活跃 Operator 的 Workarea 信息；
7. [V$SQL_WORKAREA_HISTOGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051860)：展示之前所有 workarea 执行的统计信息，如在区间1M～2M之间全 in-memory 处理的次数、one-pass 次数等；
8. [V$OB_SQL_WORKAREA_MEMORY_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051824)：用于查询 SQL workarea 总体的一些信息，包括最大可用内存、当前使用内存、当前 hold 内存等；

###### 3.2.15 加密表相关的统计信息
1. [V$OB_ENCRYPTED_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051947)：用于展示开启加密功能的表的加密状态；
2. [V$ENCRYPTED_TABLESPACES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051839)：用于展示开启加密功能的表空间的加密状态；

###### 3.2.16 事务相关的统计信息
1. [GV$LATCH](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051834)：集群所有 OBServer 节点的 Latch 信息视图；
2. [V$LATCH](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051851)：集群中所有 OBServer 节点的 Latch 信息视图；


#### 4 Oracle 租户下视图总览

##### 4.1 字典视图总览

> [!NOTE] `功能适用性`：
> 该内容仅适用于 OceanBase 数据库企业版。OceanBase 数据库社区版仅提供 MySQL 模式。

###### 4.1.1 表和视图相关
1. [[15_OceanBase/99_内部表介绍/表和视图相关_oracle_01\|表和视图相关_oracle_01]]，；
	1. 查询表的信息：*USER_TABLES*,  *ALL_TABLES*,  *DBA_TABLES*；
		1. *USER_TABLES*：：展示当前用户拥有的所有表； *ALL_TABLES*：展示用户可访问的所有表；*DBA_TABLES*：展示数据库中的所有表；
	2. 查询视图的信息：*USER_VIEWS*,  *ALL_VIEWS*,  *DBA_VIEWS*；
		1. *USER_VIEWS*：展示当前用户拥有的视图； 
		2. *ALL_VIEWS*：展示当前用户可访问的视图；
		3. *DBA_VIEWS*：展示数据库中的所有视图。其列与 `ALL_VIEWS` 中的列相同；
	3. 查询表、视图、集群的所有列的信息；
		1. `USER_TAB_COLUMNS`，`ALL_TAB_COLUMNS`，`DBA_TAB_COLUMNS`
			1. *USER_TAB_COLUMNS*：展示当前用户拥有的表、视图、集群的所有列；
			2. *ALL_TAB_COLUMNS*：展示当前用户可访问的表、视图、集群的所有列；
			3. *DBA_TAB_COLUMNS*：展示数据库中所有的表、视图、集群的所有列；
		2. `USER_TAB_COLS`，`ALL_TAB_COLS`，`DBA_TAB_COLS`
			1. *USER_TAB_COLS*：展示当前用户拥有的表、视图、集群的所有列；
			2. *ALL_TAB_COLS*：展示当前用户可访问的表、视图、集群的所有列；
			3. *DBA_TAB_COLS*：展示数据库中的所有表、视图、集群的所有列；
	4. 查询视图，表的注释：`USER_COL_COMMENTS`，`ALL_COL_COMMENTS`，`DBA_COL_COMMENTS`
		1. *USER_COL_COMMENTS*：展示当前用户拥有的视图和表的所有列注释；
		2. *ALL_COL_COMMENTS*：展示用户可访问的视图和表的所有列注释；
		3. *DBA_COL_COMMENTS*：展示数据库中所有的视图和表的所有列注释；
	5. 查询视图，表的列注释：`USER_TAB_COMMENTS`，`ALL_TAB_COMMENTS`，`DBA_TAB_COMMENTS`
		1. *USER_TAB_COMMENTS*：展示当前用户拥有的表、视图的注释；
		2. *ALL_TAB_COMMENTS*：展示当前用户可访问的表、视图的注释；
		3. *DBA_TAB_COMMENTS*：展示数据库中所有表、视图的注释；
2. 11231
	1. [USER_ALL_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051076)：展示当前用户可访问的对象表和关系表，除了 OWNER 字段之外，其他字段和 ALL_ALL_TABLES 相同；
	2. [ALL_ALL_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051361)：展示用户可访问的对象表和关系表；
	3. [DBA_ALL_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051098)：展示数据库中的对象表和关系表，它的字段与 ALL_ALL_TABLES 相同；
3. 1
	1. [USER_TABLESPACES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051393)：展示当前用户可以访问的表空间。其列与 `DBA_TABLESPACES` 中的列相同，去除了 `PLUGGED_IN` 字段；
	2. [DBA_TABLESPACES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051316)：展示数据库中的所有表空间；
4. [DBA_OB_TABLEGROUP_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051270)：展示 TABLEGROUP、TABLE 关联关系。不展示不在 TABLEGROUP 中的 TABLE；
5. [DICTIONARY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051376)：展示数据字典表和视图的描述；
6. [DICT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051322)：展示数据字典表和视图的描述；
7. [DBA_OB_TABLE_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051172)：展示表或者分区所在的位置，包括：系统表、用户表、索引表等；
8. [DBA_OB_SPATIAL_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051343)：展示空间索引列对应的数据库名、表名、类型以及 SRS 信息；


###### 4.1.2 Outline
SYS 租户，MySql 租户，Oracle 租户 已合并；



###### 4.1.3 SPM 相关
1. [DBA_SQL_PLAN_BASELINES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051088)：展示 SPM 中 SQL 的计划基线；
2. [DBA_SQL_MANAGEMENT_CONFIG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051345)：展示 SPM 中的配置参数；


###### 4.1.4 数据库基本信息
1. [DBA_CONTEXT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051239)：展示数据库中的所有上下文命名空间信息；
2. [DBA_OB_DATABASES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051099)：展示租户的数据库元信息；
3. [DBA_OB_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051128)：展示本租户的合并全局信息；
4. [DBA_OB_ZONE_MAJOR_COMPACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051198)：展示本租户各个 Zone 的合并信息；
5. [ALL_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051269)：展示用户可访问的所有数据库对象；
6. [DBA_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051249)：展示数据库所有数据库对象；
7. [USER_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051158)：展示用户拥有的所有数据库对象；
8. [ALL_DEPENDENCIES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051360)：展示数据库中所有对象之间的依赖关系；
9. [DBA_DEPENDENCIES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051209)：展示数据库中各对象之间的所有依赖关系；
10. [USER_DEPENDENCIES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051074)：展示当前用户 Schema 中各对象之间的依赖关系；
11. [DBA_OB_FREEZE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051318)：展示合并（major_freeze）产生的版本信息；


###### 4.1.5 函数和过程
1. [ALL_ARGUMENTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051278)：展示当前用户可以访问的函数和过程的参数；
2. [ALL_PROCEDURES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051123)：展示当前用户可以访问的所有函数和过程，以及相关的属性；
3. [ALL_SOURCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051120)：展示当前用户可以访问的存储对象的文本源；
4. [DBA_ARGUMENTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051160)：展示数据库中可用的函数和过程的参数。其列与 `ALL_ARGUMENTS` 中的列相同；
5. [DBA_PROCEDURES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051331)：展示数据库中可用的所有函数和过程，以及它们的相关属性。其列与 `ALL_PROCEDURES` 中的列相同；
6. [DBA_SOURCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051297)：展示数据库中所有存储对象的文本源。其列与 `ALL_SOURCE` 中的列相同；
7. [USER_ARGUMENTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051213)：展示当前用户拥有的函数和过程的参数。其列与 `ALL_ARGUMENTS` 中的列相同，去除了 `OWNER` 字段；
8. [USER_PROCEDURES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051214)：展示当前用户拥有的所有函数和过程，以及它们的关联属性。其列与 `ALL_PROCEDURES` 中的列相同，去除了 `OWNER` 字段；
9. [USER_SOURCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051377)：展示当前用户拥有的存储对象的文本源。其列与 `ALL_SOURCE` 中的列相同，去除了 `OWNER` 字段；
10. [DBA_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051312)：展示当前提交的任务总览；
11. [USER_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051344)：展示当前提交的任务总览；
12. [DBA_JOBS_RUNNING](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051387)：展示正在运行的任务；


###### 4.1.6 备份恢复相关
SYS 租户，MySql 租户，Oracle 租户 已合并；



###### 4.1.7 统计信息和直方图
1. [ALL_PART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051241)：展示当前用户可访问的分区表中一级分区的列统计信息和直方图信息；
2. [ALL_PART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051392)：展示所有一级分区的直方图相关信息；
3. [ALL_SUBPART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051106)：展示当前用户可访问的分区表中二级分区的列统计信息和直方图信息；
4. [ALL_SUBPART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051281)：展示所有二级分区的直方图相关信息；
5. [ALL_TAB_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051211)：展示当前用户可访问表的列统计信息和直方图信息；
6. [ALL_TAB_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051378)：展示所有表级的直方图相关信息；
7. [ALL_TAB_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051262)：展示当前用户可访问表的优化器统计信息；
8. [DBA_PART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051256)：展示数据库的分区表中一级分区的列统计信息和直方图信息；
9. [DBA_PART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051197)：展示当前数据库的一级分区的直方图相关信息；
10. [DBA_SUBPART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051146)：展示数据库的分区表中二级分区的列统计信息和直方图信息；
11. [DBA_SUBPART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051122)：展示当前数据库的二级分区的直方图相关信息；
12. [DBA_TAB_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051095)：展示数据库中所有表的列统计信息和直方图信息；
13. [DBA_TAB_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051321)：展示当前数据库的表级的直方图相关信息；
14. [DBA_TAB_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051251)：展示数据库中所有表的优化器统计信息；
15. [USER_PART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051346)：展示当前用户的分区表中一级分区的列统计信息和直方图信息；
16. [USER_PART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051200)：展示当前用户的一级分区的直方图相关信息；
17. [USER_SUBPART_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051353)：展示当前用户的分区表中二级分区的列统计信息和直方图信息；
18. [USER_SUBPART_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051103)：展示当前用户的二级分区的直方图相关信息；
19. [USER_TAB_COL_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051225)：展示当前用户的表的列统计信息和直方图信息；
20. [USER_TAB_HISTOGRAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051369)：展示当前用户的表级的直方图相关信息；
21. [USER_TAB_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051388)：展示当前用户的所有表的优化器统计信息；
22. [ALL_TAB_STATS_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051207)：展示当前用户可访问的所有表的表统计修改历史记录；
23. [DBA_TAB_STATS_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051272)：展示数据库中所有表的表统计修改历史记录；
24. [USER_TAB_STATS_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051300)：展示当前用户拥有的所有表的表统计修改历史记录；
25. [ALL_IND_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051347)：展示当前用户可访问的表上的索引的优化器统计信息；
26. [DBA_IND_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051363)：展示数据库中所有索引的优化器统计信息；
27. [USER_IND_STATISTICS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051119)：展示当前用户拥有的表的索引的优化器统计信息；
28. [ALL_TAB_MODIFICATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051194)：展示当前用户可访问的表的自上次在该表上收集统计信息以来的修改信息；
29. [DBA_TAB_MODIFICATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051204)：展示数据库中所有表的自上次在该表上收集统计信息以来的修改信息；
30. [USER_TAB_MODIFICATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051352)：展示当前用户拥有的表的自上次在该表上收集统计信息以来的修改信息；


###### 4.1.8 用户及权限
1. [ALL_USERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051313)：展示当前用户可见的所有数据库用户；
2. [DBA_USERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051168)：展示数据库的所有用户；
3. [USER_USERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051288)：展示当前用户的信息；
4. [ALL_COL_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051169)：当前用户为其对象所有者，授予者或被授予者的列对象权限。已启用角色或被授予者是 PUBLIC 的列对象权限；
5. [ALL_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051188)：_当前用户为对象的拥有者，或为对象权限的授予者，或为对象权限的被授予者。_ 对象权限的被授予者为已激活角色或 PUBLIC 角色；
6. [DBA_COL_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051294)：展示数据库中的所有列对象授予；
7. [DBA_SYS_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051240)：展示授予用户和角色的系统特权；
8. [DBA_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051274)：展示数据库中的所有对象授予；
9. [USER_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051287)：展示当前用户拥有的对象权限；
10. [USER_COL_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051191)：展示当前用户是对象所有者、授予者或被授予者的列对象授予；
11. [USER_ROLE_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051349)：展示授予当前用户的角色；
12. [USER_SYS_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051167)：展示授予当前用户的系统权限；
13. [ROLE_TAB_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051242)：展示授予角色的表特权。仅提供关于用户可以访问的角色的信息；
14. [ROLE_SYS_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051244)：展示授予角色的系统特权；
15. [ROLE_ROLE_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051271)：展示授予其他角色的角色。只提供关于用户可以访问的角色的信息；
16. [DBA_ROLE_PRIVS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051286)：展示授予所有用户的角色和数据库中的角色；
17. [DBA_ROLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051283)：展示数据库中存在的所有角色；
18. [ALL_DIRECTORIES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051101)：展示当前用户可访问的所有目录；
19. [DBA_DIRECTORIES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051196)：展示当前用户可访问的所有目录；


###### 4.1.9 约束
1. [ALL_CONSTRAINTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051077)：展示用户可访问的所有表的约束信息；
2. [ALL_CONS_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051252)：展示用户可访问的所有表的约束中的列信息；
3. [DBA_CONSTRAINTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051104)：展示数据库中所有表的约束信息；
4. [DBA_CONS_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051379)：展示数据库中所有表的约束中的列信息；
5. [USER_CONSTRAINTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051143)：展示用户拥有的所有表的约束信息；
6. [USER_CONS_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051108)：展示用户拥有的所有表的约束中的列信息；


###### 4.1.10 审计
1. [ALL_DEF_AUDIT_OPTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051261)：展示任何对象默认被审计的审计条件；
2. [AUDIT_ACTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051296)：展示审计记录中 Action ID 和对应的名称；
3. [DBA_AUDIT_EXISTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051389)：展示 `AUDIT EXISTS` 和 `AUDIT NOT EXISTS` 产生的审计跟踪条目；
4. [DBA_AUDIT_OBJECT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051152)：展示数据库中所有对象的审计跟踪记录；
5. [DBA_AUDIT_SESSION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051230)：展示所有关于 `CONNECT` 和 `DISCONNECT` 的审计跟踪记录；
6. [DBA_AUDIT_STATEMENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051290)：展示数据库中关于 `GRANT`、`REVOKE`、`AUDIT`、`NOAUDIT` 和 `ALTER SYSTEM` 语句的审计跟踪记录；
7. [DBA_AUDIT_TRAIL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051394)：展示所有审计跟踪条目；
8. [DBA_OBJ_AUDIT_OPTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051261)：展示对象级别审计详细配置；
9. [DBA_STMT_AUDIT_OPTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051126)：展示语句级别审计的详细配置；
10. [STMT_AUDIT_OPTION_MAP](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051263)：展示语句级别审计的 Type 及对应的名称和优先级；
11. [USER_AUDIT_OBJECT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051289)：展示当前用户可访问的对象的审计跟踪记录；
12. [USER_AUDIT_SESSION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051372)：展示当前用户的连接和断开的审计跟踪记录；
13. [USER_AUDIT_STATEMENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051219)：展示当前用户发出的 `GRANT`、`REVOKE`、`AUDIT`、`NOAUDIT` 和 `ALTER SYSTEM` 语句的审计跟踪条目；
14. [USER_AUDIT_TRAIL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051157)：展示与当前用户相关的标准审计跟踪条目；


###### 4.1.11 索引
1. [[15_OceanBase/99_内部表介绍/Oracle 租户索引信息_01\|Oracle 租户索引信息_01]]，；
	1. `查看索引信息`
		1. *USER_INDEXES*：展示用户拥有的所有表的索引信息；
		2. *ALL_INDEXES*：展示用户可访问的所有表的索引的索引列信息；
		3. *DBA_INDEXES*：展示数据库所有表的索引信息；
	2. `查询索引列信息`
		1. *USER_IND_COLUMNS*：展示用户拥有的所有表的索引的索引列信息；
		2. *ALL_IND_COLUMNS*：展示用户可访问的所有表的索引的索引列信息；
		3. *DBA_IND_COLUMNS*：展示数据库所有表的索引的索引列信息；
	3. 123
		1. *USER_IND_EXPRESSIONS*：展示用户在数据库中所拥有的 function-based 索引的信息；
		2. *ALL_IND_EXPRESSIONS*：展示用户在数据库中可访问的 function-based 索引的信息；
		3. *DBA_IND_EXPRESSIONS*：展示数据库中所有 function-based 索引的信息；
3. 123
	1. [USER_IND_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051121)：1. 展示当前用户为所有者的数据库中索引分区的信息，与 `ALL_IND_PARTITIONS`、`DBA_IND_PARTITIONS` 相比没有`INDEX_OWNER` 字段；
	2. [ALL_IND_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051173)：展示用户可访问的数据库中索引分区的信息；
	3. [DBA_IND_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051306)：展示数据库中所有索引分区的信息；
4. 123
	1. [USER_IND_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051195)：展示数据库中用户所拥有的所有索引子分区的信息，与 `DBA_IND_SUBPARTITIONS`、`ALL_IND_SUBPARTITIONS` 相比没有 `INDEX_OWNER` 字段；
	2. [ALL_IND_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051254)：展示数据库中用户可以访问的所有索引子分区的信息；
	3. [DBA_IND_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051212)：展示数据库中所有索引子分区的信息；
5. 123
	1. [ALL_PART_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051093)：展示当前用户可访问的分区索引的分区信息；
	2. [USER_PART_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051080)：展示当前用户的分区索引的分区信息；


###### 4.1.12 分区
1. [ALL_PART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051094)：展示用户可访问的所有分区对象中分区键包含的列；
2. [ALL_PART_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051293)：展示当前用户可访问的分区表的分区信息；
3. [ALL_SUBPARTITION_TEMPLATES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051161)：展示所有子分区模版描述信息；
4. [ALL_SUBPART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051133)：展示用户可访问的所有分区对象中子分区键包含的列；
5. [ALL_TAB_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051277)：展示所有分区表的多级分区信息、分区存储参数和分区统计信息；
6. [ALL_TAB_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051141)：展示 `DBMS_STATS` 包为当前用户可访问的分区生成的分区级别的分区信息、分区存储参数和分区统计信息；
7. [DBA_PART_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051298)：展示所有的分区索引的分区信息；
8. [DBA_PART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051203)：展示数据库中所有分区对象中分区键包含的列；
9. [DBA_PART_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051111)：展示当前用户可访问的分区表的分区信息；
10. [DBA_SUBPARTITION_TEMPLATES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051305)：展示所有子分区模版描述信息；
11. [DBA_SUBPART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051310)：展示数据库中所有分区对象中子分区键包含的列；
12. [DBA_TAB_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051138)：展示当前用户可访问的多级分区信息、分区存储参数和分区统计信息；
13. [DBA_TAB_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051336)：展示子分区名称、表和所属分区的名称、其存储属性以及 `DBMS_STATS` 包生成的统计信息；
14. [USER_PART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051150)：展示当前用户拥有的所有分区对象中分区键包含的列；
15. [USER_PART_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051257)：展示当前用户的分区表的分区信息；
16. [USER_SUBPARTITION_TEMPLATES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051264)：展示所有子分区模版描述信息；
17. [USER_SUBPART_KEY_COLUMNS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051367)：展示当前用户拥有的所有分区对象中子分区键包含的列；
18. [USER_TAB_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051235)：展示当前用户拥有的每个表子分区的子分区名、表的名称和它所属的分区以及它的存储属性；
19. [USER_TAB_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051140)：展示当前用户的多级分区信息、分区存储参数和分区统计信息；


###### 4.1.13 Session 相关
1. [DBA_OB_DEADLOCK_EVENT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051370)：展示死锁事件的历史记录，该视图在非 root 租户下展示本租户的历史记录；


###### 4.1.14 序列
1. [ALL_SEQUENCES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051260)：展示用户可访问的所有 SEQUENCE；
2. [DBA_SEQUENCES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051304)：展示数据库中所有 SEQUENCE；
3. [USER_SEQUENCES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051303)：展示当前用户拥有的所有 SEQUENCE；
4. [TENANT_VIRTUAL_GLOBAL_VARIABLE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051315)：展示全局变量信息；
5. [TENANT_VIRTUAL_SESSION_VARIABLE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051237)：展示 Session 的变量信息；


###### 4.1.15 同义词
1. [ALL_SYNONYMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051391)：展示用户可访问的所有同义词；
2. [DBA_SYNONYMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051134)：展示数据库中所有同义词；
3. [USER_SYNONYMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051338)：展示当前用户所拥有的同义词；


###### 4.1.16 触发器
1. [ALL_TRIGGERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051364)：展示当前用户拥有的表上触发器与描述。如果用户具有 CREATE ANY TRIGGER 权限，则该视图描述数据库中的所有触发器；
2. [DBA_TRIGGERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051265)：展示数据库中的所有触发器；
3. [USER_TRIGGERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051116)：展示当前用户拥有的触发器；


###### 4.1.17 回收站
1. [DBA_RECYCLEBIN](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051163)：展示数据库中所有回收站的信息；
2. [USER_RECYCLEBIN](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051385)：展示当前用户拥有的回收站的信息。其列与 `DBA_RECYCLEBIN` 中的列相同，去除了 `OWNER` 字段；


###### 4.1.18 资源组
1. [DBA_RSRC_GROUP_MAPPINGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051105)：展示用户和资源组之间的绑定关系；
2. [DBA_RSRC_CONSUMER_GROUPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051233)：展示数据库中所有资源组的信息；
3. [DBA_RSRC_PLANS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051228)：展示系统定义的所有资源管理计划；
4. [DBA_RSRC_PLAN_DIRECTIVES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051250)：展示数据库中每组资源计划指令的详细信息；


###### 4.1.19 NLS
1. [NLS_DATABASE_PARAMETERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051366)：展示 DataBase 级别（ OceanBase 数据库租户级）的 NLS 参数；
2. [NLS_INSTANCE_PARAMETERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051341)：展示实例级别（租户级别）的 NLS 参数；
3. [NLS_SESSION_PARAMETERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051135)：展示 Session 级别的 NLS 参数；


###### 4.1.20 用户自定义类型
1. [ALL_COLL_TYPES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051342)：展示数据库中可用的用户自定义集合类型。其列与 `DBA_COLL_TYPES` 中的列相同；
2. [ALL_TYPES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051227)：展示数据库中可用的用户自定义类型；
3. [ALL_TYPE_ATTRS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051082)：展示数据库中可用的用户自定义类型的属性信息；
4. [DBA_COLL_TYPES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051307)：展示数据库中可用的用户自定义集合类型。其列与 `ALL_COLL_TYPES` 中的列相同；
5. [DBA_TYPES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051335)：展示数据库中可用的用户自定义类型；
6. [DBA_TYPE_ATTRS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051291)：展示数据库中可用的用户自定义类型的属性信息；
7. [USER_COLL_TYPES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051222)：展示当前用户可用的用户自定义集合类型；
8. [USER_TYPES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051334)：展示当前用户的用户自定义类型；
9. [USER_TYPE_ATTRS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051374)：展示当前用户可用的用户自定义类型的属性信息；


###### 4.1.21 存储对象
1. [ALL_ERRORS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051220)：展示用户可以访问的当前存储对象的错误；
2. [DBA_ERRORS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051137)：展示数据库中的当前存储对象的错误；
3. [USER_ERRORS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051295)：展示用户可访问的当前存储对象的错误；
4. [DBA_SEGMENTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051328)：展示数据库中所有段分配的存储信息；
5. [USER_SEGMENTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051156)：展示当前用户下的对象拥有的段分配的存储信息；


###### 4.1.22 PROFILE
1. [ALL_PROFILES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051084)：展示所有的简介和这些简介的限制；
2. [DBA_PROFILES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051268)：展示所有的简介和这些简介的限制；
3. [USER_PROFILES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051382)：展示所有的简介和这些简介的限制；


###### 4.1.23 副本和日志相关
1. [DBA_OB_LS_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051359)：展示本租户的日志流（ LS ）副本分布信息；
2. [DBA_OB_TABLEGROUPS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051396)：展示表组的元信息，主要是展示表组的分区信息；
3. [DBA_OB_TABLEGROUP_PARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051202)：展示分区的表组的一级分区信息；
4. [DBA_OB_TABLEGROUP_SUBPARTITIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051174)：展示分区的表组的二级分区信息；
5. [DBA_OB_TABLET_REPLICAS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051171)：
	1. 展示本租户的所有 TABLET 副本信息，每个 TABLET 副本由联合键唯一确定：`<TENANT_ID, TABLET_ID, SVR_IP, SVR_PORT, LS_ID>`；
6. [DBA_OB_TABLET_TO_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051102)：展示本租户的所有 Tablet 列表以及 Tablet 所属的日志流信息；
7. [DBA_OB_LS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051131)：展示日志流的状态和恢复进度；


###### 4.1.24 WR 相关
1. [DBA_WR_SNAPSHOT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051248)：展示本租户的 `SNAPSHOT` 信息；
2. [DBA_WR_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051232)：展示本租户持久化后的 ASH 数据；
3. [DBA_WR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051187)：展示本租户的系统统计项的名称；
4. [DBA_WR_SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051190)：展示本租户的系统统计项的详细信息；
5. [DBA_WR_CONTROL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051238)：展示 WR 相关的配置信息|
6. [DBA_WR_SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051144)：用于展示本租户的持久化后的租户级等待事件信息；
7. [DBA_WR_EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051330)：用于展示本租户的持久化后的租户级等待事件名称；
8. [DBA_WR_SQLTEXT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051170)：用于展示本租户 WR 采集的 SQL 文本；


###### 4.1.25 SQLSTAT 相关
1. [DBA_WR_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051285)：用于展示本租户执行过的 SQL 的基本性能统计数据；


###### 4.1.26 SQLSTAT 相关
1. [DBA_WR_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051282)：展示了本租户 Time Model 统计项的 WR 数据；


###### 4.1.27 负载均衡相关
1. [DBA_OB_TRANSFER_PARTITION_TASKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051136)：展示了本租户下当前正在处理的 Transfer Partition 任务；
2. [DBA_OB_TRANSFER_PARTITION_TASK_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051115)：展示了本租户已执行的 Transfer Partition 任务历史；


###### 4.1.28 调度程序
1. [DBA_SCHEDULER_JOBS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051127)：展示当前用户可访问的调度程序作业信息；
2. [DBA_SCHEDULER_PROGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051325)：展示数据库中所有调度程序信息；
3. [DBA_SCHEDULER_JOB_RUN_DETAILS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051365)：展示租户下所有调度程序作业的信息；

###### 4.1.29 DBLINK 相关
1. [ALL_DB_LINKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051210)：展示当前用户可访问的所有 DBLINK；
2. [DBA_DB_LINKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051375)：展示当前租户下所有已创建的 DBLINK；
3. [USER_DB_LINKS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051223)：展示当前用户所创建的 DBLINK；

###### 4.1.30 暂不支持的视图
1. [ALL_METHOD_PARAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051079)：展示用户可访问的对象类型的方法参数；
2. [ALL_SCHEDULER_JOB_ARGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051075)：展示用户可查的调度作业参数信息；
3. [ALL_SCHEDULER_PROGRAM_ARGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051177)：展示用户可查的调度程序参数信息；
4. [ALL_TYPE_METHODS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051085)：展示用户可以访问的对象类型的方法；
5. [DBA_METHOD_PARAMS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051299)：展示数据库中的对象类型的方法参数；
6. [DBA_SCHEDULER_JOB_ARGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051205)：展示数据库中的调度作业参数信息；
7. [USER_SCHEDULER_JOB_ARGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051311)：展示当前用户拥有的调度作业参数信息；
8. [DBA_SCHEDULER_PROGRAM_ARGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051183)：展示数据库中的调度程序参数信息；
9. [USER_SCHEDULER_PROGRAM_ARGS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051221)：展示当前用户拥有的调度程序参数信息；
10. [DBA_TYPE_METHODS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051276)：展示数据库中对象类型的方法；
11. [USER_TYPE_METHODS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051258)：展示用户可以访问的对象类型的方法，除了不含有 OWNER 字段，字段信息和 ALL_TYPE_METHODS 一致；

##### 4.2 性能视图总览
###### 4.2.1 ASH 相关
1. [GV$OB_ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051063)：用于展示活跃会话历史记录；


###### 4.2.2 统计信息
1. [GV$OB_KVCACHE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050962)：展示每台 OBServer 节点上的 KVCACHE 信息；
2.
3. [GV$OB_SERVER_SCHEMA_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051003)：展示所有 Server 上的 Schema 信息；
4. [V$OB_SERVER_SCHEMA_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050959)：展示所有 Server 上的 Schema 信息；
5. [GV$OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051042)：展示租户所在的 OBServer 节点的 Unit 信息，系统租户看到本集群所有 OBServer 节点的 Unit 信息；
6. [GV$SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050946)：展示所有服务器上系统级别的统计事件信息；
7. [GV$DML_STATS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051023)：展示当前用户表中的 DML 信息；
8. [V$DML_STATS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051050)：展示当前用户表中的 DML 信息；
9. [V$GLOBALCONTEXT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051013)：展示当前会话可访问的全局上下文属性的 `CLIENT_IDENTIFIER` 值；
10. [V$OB_KVCACHE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051044)：展示每台 OBServer 节点上的 KVCACHE 信息；
11. 
12. [V$SYSSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051038)：展示当前服务器上系统级别的统计事件信息；
13. [V$OB_UNITS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050943)：展示本 OBServer 节点的 Unit 信息；
14. [V$VERSION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050974)：查看数据库产品所有组件的名称和版本信息；
15. [V$STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050952)：用于展示统计项的名称；


###### 4.2.3 实例相关
1. [GV$INSTANCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051059)：展示了当前数据库实例的状态；
2. [V$INSTANCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051062)：展示了当前数据库实例的状态；


###### 4.2.4 内存相关
1. [GV$OB_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051069)：展示所有服务器上每个租户的内存使用状况；
2. [GV$OB_MEMSTORE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051021)：展示所有服务器上所有租户的 MemTable 的内存使用状况；
3. [GV$OB_SQL_WORKAREA_MEMORY_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051029)：表示查询 SQL Workarea 总体的一些信息，包括最大可用内存、当前使用内存、当前 Hold 内存等；
4. [GV$OB_SSTABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051040)：展示租户的 SSTable 信息；
5. [GV$OB_MEMSTORE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050980)：展示所有服务器上所有租户的所有分区的 MemTable 的详细信息；
6. [V$OB_MEMSTORE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051026)：展示所有服务器上所有租户的所有分区的 MemTable 的详细信息；
7. [V$OB_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050977)：展示租户级别的内存统计信息；
8. [V$OB_MEMSTORE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050991)：展示当前服务器上所有租户的 MemTable 的内存使用状况；
9. [V$OB_SSTABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050965)：展示租户的 SSTable 信息；
10. [GV$OB_TENANT_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051070)：展示集群上每个租户的内存使用状况；
11. [V$OB_TENANT_MEMORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051031)：展示当前 observer 进程中每个租户的内存使用 状况；


###### 4.2.5 缓存相关

3. 

8. [V$RSRC_PLAN](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051053)：展示当前活跃租户的资源管理计划；


###### 4.2.6 日志相关
1. [GV$OB_LOG_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051049)：展示 Palf 的状态；
2. [V$OB_LOG_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051039)：展示 Palf 的状态；


###### 4.2.7 监控信息
1. [GV$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051030)：展示所有 OBServer 节点上的 PX 线程使用情况；
2. [GV$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051057)：给 Prepared Statement 提供监控功能，包含 PS Cache 中所有 Prepare Statement 的基本信息；
3. [GV$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051041)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 Item 数目；
4. [GV$OB_DTL_INTERM_RESULT_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050997)：展示 DTL 中间结果管理器的监控信息；
5. [V$OB_DTL_INTERM_RESULT_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051061)：展示 DTL 中间结果管理器的监控信息；
6. [V$OB_PX_TARGET_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050953)：展示 OBServer 节点上的 PX 线程使用情况；
7. [V$OB_PS_ITEM_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050966)：给 Prepared Statement提供监控功能，包含 PS Cache 中所有 Prepared Statement 的基本信息；
8. [V$OB_PS_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051001)：监控 PS Cache 的整体状态，包括命中率、内存占用、缓存的 Item 数目；
9. [V$SQL_MONITOR_STATNAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051010)：展示 `GV$SQL_PLAN_MONITOR` 的 `OTHERSTAT_X_ID` 字段中数字的含义；


###### 4.2.8 Session 相关
1. [GV$OB_PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051028)：展示租户所在的所有 OBServer 节点的会话信息；
2. [GV$SESSION_WAIT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051000)：展示所有服务器上所有 Session 的当前或者上一次等待事件的一些信息，包括等待事件名称、等待耗时等；
3. [GV$SESSION_WAIT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050941)：展示所有服务器上所有 Session 的最近 10 次等待事件的信息；
4. [GV$SESSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051015)：展示所有服务器上的 Session 级别的一些统计信息；
5. [GV$ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051022)：展示活跃会话历史记录；
6. [V$ACTIVE_SESSION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051068)：展示活跃会话历史记录；
7. [V$NLS_PARAMETERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050969)：查看 Session 级别的 NLS 参数；
8. [V$OB_PROCESSLIST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051019)：展示本 OBServer 节点的会话信息；
9. [V$SESSION_WAIT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050964)：展示当前服务器上所有 Session 的当前或者上一次等待事件的信息，包括等待事件名称、等待耗时等；
10. [V$SESSION_WAIT_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051071)：展示当前服务器上所有 Session 的最近 10 次等待事件的信息；
11. [V$SESSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051047)：展示 Session 级别的一些统计信息；
12. [V$EVENT_NAME](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051067)：展示租户下的各种事件定义；


###### 4.2.9 SQL 相关的统计信息
3. [GV$SQL_PLAN_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051007)：展示所有 OBServer 节点慢查询的 Plan 层面的统计，每个慢查询都会有一条统计信息，同时记录该 Plan 的 Trace 信息；
4. [V$SQL_PLAN_MONITOR](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050973)：展示所有 OBServer 节点慢查询的 Plan 层面的统计，每个慢查询都会有一条统计信息，同时记录该 Plan 的 Trace 信息；
5. [GV$OB_FLT_TRACE_CONFIG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051020)：展示全链路诊断的诊断信息；


###### 4.2.10 线程
1. [GV$OB_PX_WORKER_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051005)：在并行执行场景中，查看每个线程的执行信息；
2. [V$OB_PX_WORKER_STAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051054)：在并行执行场景中，查看每个线程的执行信息；


###### 4.2.11 Workarea
1. [GV$SQL_WORKAREA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050983)：之前受 SQL 自动内存管理负责执行的所有 Operator 的 Workarea 统计信息；
2. [GV$SQL_WORKAREA_ACTIVE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050957)：当前活跃 Operator 的 Workarea 信息；
3. [GV$SQL_WORKAREA_HISTOGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051034)：展示之前所有 Workarea 执行的统计信息；
4. [V$OB_SQL_WORKAREA_MEMORY_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051073)：查询 SQL Workarea 总体的一些信息，包括最大可用内存、当前使用内存、当前 Hold 内存等；
5. [V$SQL_WORKAREA](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050951)：统计之前受 SQL 自动内存管理负责执行过的所有 Operator 的 Workarea 统计信息；
6. [V$SQL_WORKAREA_ACTIVE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051048)：当前活跃 Operator 的 Workarea 信息；
7. [V$SQL_WORKAREA_HISTOGRAM](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051072)：展示之前所有 Workarea 执行的统计信息；


###### 4.2.12 事务相关
1. [GV$OB_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050942)：展示租户的 OBServer 级合并进度信息；
2. [GV$OB_COMPACTION_SUGGESTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050979)：展示 COMPACTION 的建议信息；
3. [GV$OB_COMPACTION_DIAGNOSE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050944)：展示 COMPACTION 的诊断信息；
4. [GV$OB_TABLET_COMPACTION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050982)：展示 TABLET 级 COMPACTION 的历史信息；
5. [GV$OB_TABLET_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051064)：展示 TABLET 级 COMPACTION 的进度信息；
6. [GV$OB_TRANSACTION_PARTICIPANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050994)：展示所有 OBServer 节点上的事务参与者信息；
7. [GV$SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050970)：展示所有服务器上租户系统级别的一些等待事件，统计每一类等待事件的发生次数、等待时间、超时次数等；
8. [GV$GLOBAL_TRANSACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051056)：显示当前活跃的全局事务的信息；
9. [V$OB_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051037)：展示租户的 Server 级合并进度信息；
10. [V$OB_COMPACTION_SUGGESTIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050956)：展示 COMPACTION 的建议信息；
11. [V$OB_COMPACTION_DIAGNOSE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051025)：展示 COMPACTION 的诊断信息；
12. [V$OB_TABLET_COMPACTION_HISTORY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050950)：展示 TABLET 级 COMPACTION 的历史信息；
13. [V$OB_TABLET_COMPACTION_PROGRESS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051009)：展示 TABLET 级 COMPACTION 的进度信息；
14. [V$OB_TRANSACTION_PARTICIPANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050960)：展示所有 OBServer 节点上的事务参与者信息；
15. [V$SYSTEM_EVENT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051018)：展示当前服务器上租户系统级别的一些等待事件，统计每一类等待事件的发生次数、等待时间、超时次数等；
16. [V$GLOBAL_TRANSACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050954)：显示当前活跃的全局事务的信息；
17. [GV$OB_GLOBAL_TRANSACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051035)：展示集群中所有 XA 事务的信息；
18. [V$OB_GLOBAL_TRANSACTION](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051008)：展示以当前节点为原始 Scheduler 的所有 XA 事务的信息；


###### 4.2.13 时区
1. [V$TIMEZONE_NAMES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051002)：显示有效的时区名称；


###### 4.2.14 加密表
1. [V$OB_ENCRYPTED_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050955)：展示开启加密功能的表的加密状态；
2. [V$ENCRYPTED_TABLESPACES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050968)：展示开启加密功能的表空间的加密状态；


###### 4.2.15 DBLINK
1. [GV$DBLINK](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051033)：展示该视图上发出的查询会话打开后的所有数据库链接（ `IN_TRANSACTION=YES` 的链接）。这些数据库链接必须在关闭之前提交或回滚；
2. [V$DBLINK](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051024)：展示该视图上发出的查询会话打开后的所有数据库链接（ `IN_TRANSACTION=YES` 的链接）。这些数据库链接必须在关闭之前提交或回滚；


###### 4.2.16 SQLSTAT 相关
1. [GV$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051058)：用于展示集群中 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；
2. [V$OB_SQLSTAT](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050998)：用于展示当前节点上 SQL 的基本性能统计数据，每个 SQL 语句为一行（即每个 `SQL_ID` + `Plan_Hash` 的唯一值为一行）；

###### 4.2.17 Time Model 相关
1. [GV$OB_SESS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001051006)：用于展示集群中 Session 级别的 Time Model 统计项；
2. [V$OB_SESS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050989)：用于展示当前节点上 Session 级别的 Time Model 统计项；
3. [GV$OB_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050995)：用于展示集群中租户级别的 Time Model 统计项；
4. [V$OB_SYS_TIME_MODEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050949)：用于展示当前节点上租户级别的 Time Model 统计项；


### 参考文档
1. *3.x 与 4.x 视图变更*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050034)，；
2. *系统视图总览*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050041)，；


#### 1 字典视图
1. [[15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_server_stat\|oceanbase.__all_virtual_server_stat]]，；
2. [[15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_trans_stat\|oceanbase.__all_virtual_trans_stat]]，；
3. [[15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_ddl_operation\|oceanbase.__all_virtual_ddl_operation]]，用来查看集群租户 DDL 信息，该表主要记录 DDL 修改记录；


#### 2 性能视图
1.  [[15_OceanBase/99_内部表介绍/GV, OB_TRANSACTION_PARTICIPANTS 视图\|GV, OB_TRANSACTION_PARTICIPANTS 视图]]，；
1.  [[15_OceanBase/99_内部表介绍/GV, MEMORY，GV, OB_MEMORY\|GV, MEMORY，GV, OB_MEMORY]]，；
1.  [[15_OceanBase/99_内部表介绍/DBA_RECYCLEBIN 表\|DBA_RECYCLEBIN 表]]，；
1.  [[15_OceanBase/99_内部表介绍/USER_RECYCLEBIN 表\|USER_RECYCLEBIN 表]]，；


3. [[15_OceanBase/99_内部表介绍/gv, sysstat\|gv, sysstat]]，；




