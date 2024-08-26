---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/查询 OceanBase 转储信息_V4.x 版本/","dgPassFrontmatter":true}
---



### 查询 OceanBase 转储信息_V4.x 版本

#### 1 查询 OceanBase 转储信息
触发转储后，您可以通过视图查看转储进度和转储历史；

OB 在 *V4.0.0 版本* 中新增了 `v$OB_MERGE_INFO` 视图，该视图详细情况，请参见：[V$OB_MERGE_INFO](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000885232)，；

```sql
-- 查询集群合并
SELECT * FROM oceanbase.gv$OB_MERGE_INFO WHERE SVR_IP='100.64.132.106' ORDER BY START_TIME LIMIT 10; 
```

##### 1.1 查看转储进度
OB 在 *V4.0.0 版本* 中开始新增了 `GV$OB_TABLET_COMPACTION_PROGRESS` 视图，该视图用于展示 Tablet 级别 Compaction 任务的进度信息，且仅展示正在运行的任务，任务结束后就不再展示；该视图详细介绍，请参见：*GV$OB_TABLET_COMPACTION_PROGRESS*：[V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699212)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820476)，；

从 V4.3.1 版本开始支持用户租户查询转储进度；

```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；
	-- 租户管理员登录集群的 `sys` 租户或用户租户；

-- 2. 查看转储进度，可以查看转储未完成的数据量及预期完成时间等信息；
	-- 系统租户查看转储进度(使用 root 用户登录集群的 sys 租户)
	    SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_PROGRESS WHERE TYPE='MINI_MERGE'\G
	    SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_PROGRESS WHERE TYPE='MINI_MERGE'\G;
		SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_PROGRESS 
		WHERE TYPE='MINI_MERGE' AND TENANT_ID = 1001 and SVR_IP ='' and SVR_PORT = ''
		ORDER BY CREATE_TIME DESC LIMIT 10\G; 
	    
	-- 用户租户查看转储进度(用户租户管理员登录集群)(V4.3.1 版本开始支持)
		obclient> SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_PROGRESS WHERE TYPE='MINI_MERGE'\G   -- MySQL 模式
		obclient> SELECT * FROM SYS.GV$OB_TABLET_COMPACTION_PROGRESS WHERE TYPE='MINI_MERGE'\G         -- Oracle 模式
		
/* 部分字段介绍：
	TYPE：表示 Compaction 任务的类型。
		MINOR_MERGE：表示 Minor，多个 Mini SSTable 合成一个 Minor SSTable；
		MINI_MERGE：表示转储，冻结 MemTable 通过转储变成 Mini SSTable；
		MEDIUM_MERGE：表示分区级合并；
		MAJOR_MERGE：表示合并；
		META_MAJOR_MERGE ：Buffer 表转储；
	STATUS：表示任务状态。任务正在运行中时，该字段值为 NODE_RUNNING；
	DATA_SIZE：表示需要转储的总数据量；
	UNFINISHED_DATA_SIZE：表示转储未完成的数据量；
	ESTIMATED_FINISH_TIME：表示预计完成时间；
*/
```
根据查询结果，对于未出现在该视图中的 Tablet 或长时间未完成的 Tablet，可以进一步查看诊断视图 `GV$OB_COMPACTION_DIAGNOSE_INFO`，确认是否有异常情况出现；有关该视图中字段的详细介绍，请参见 *GV$OB_COMPACTION_DIAGNOSE_INFO*：[V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699203)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820422)；


##### 1.2 查看转储历史
OB 在 *V4.0.0 版本* 中开始新增了 `GV$OB_TABLET_COMPACTION_HISTORY` 视图，该视图用于展示 Tablet 级别 Compaction 的历史信息；该视图详细介绍，请参见：[V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699211)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820420)，；

从 V4.3.1 版本开始支持用户租户查询转储进度；

```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；
	-- 租户管理员登录集群的 `sys` 租户或用户租户；
    
-- 2. 查看转储历史；
	-- 2.1 系统租户查看转储历史
		-- 查看表级的转储历史(使用 root 用户登录集群的 sys 租户)
		SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_HISTORY WHERE TYPE='MINI_MERGE'\G
		SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_HISTORY WHERE TYPE='MINI_MERGE' and TABLET_ID=200001\G;
	
		-- 查询租户表各种 COMPACTION 的次数
		SELECT type,count(*) as Num FROM oceanbase.GV$OB_TABLET_COMPACTION_HISTORY
		where tenant_id=1002 and TABLET_ID=200001 and svr_ip=''
		group by type

	-- 2.2 用户租户查看转储历史
		-- (用户租户管理员登录集群)(V4.3.1 版本开始支持)
		SELECT * FROM oceanbase.GV$OB_TABLET_COMPACTION_HISTORY WHERE TYPE='MINI_MERGE'\G  -- MySQL 模式
		SELECT * FROM SYS.GV$OB_TABLET_COMPACTION_HISTORY WHERE TYPE='MINI_MERGE'\G        -- Oracle 模式
```


### 参考文档



