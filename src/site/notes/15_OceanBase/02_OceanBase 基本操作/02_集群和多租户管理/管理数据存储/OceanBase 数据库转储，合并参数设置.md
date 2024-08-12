---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 数据库转储，合并参数设置/","dgPassFrontmatter":true}
---


### 1. OceanBase 数据库转储管理

转储，合并相关配置项介绍：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍#1.6 转储，合并相关\|OceanBase 配置项，变量详细介绍#1.6 转储，合并相关]]，；

#### 1 修改转储配置
##### 1.1 通过 SQL 语句修改转储配置
```sql
-- 通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'minor_compact_trigger';
SHOW PARAMETERS LIKE 'major_compact_trigger';
SHOW PARAMETERS LIKE 'freeze_trigger_percentage';
SHOW PARAMETERS LIKE 'memstore_limit_percentage';

-- 修改转储配置
ALTER SYSTEM SET minor_compact_trigger=2;
ALTER SYSTEM SET major_compact_trigger=10;
ALTER SYSTEM SET freeze_trigger_percentage=20;
ALTER SYSTEM SET memstore_limit_percentage=50; 
```

##### 1.2 转储参数设置


#### 2 修改合并配置
##### 2.1 通过 SQL 语句修改合并配置

> [!NOTE] 注意：
> 集群级配置项需要在 `sys` 租户下设置；

```sql
-- 1. 使用 root 用户登录到数据库的 sys 租户【V4.0.0 之前版本】 
	-- 在 V4.0.0 版本中，major_freeze_duty_time 参数调整为【租户级】；租户管理员登录到数据库(V4.0.0 及之后版本)；

-- 2. 通过以下 SQL 语句修改合并配置；
	obclient> SHOW PARAMETERS LIKE 'major_freeze_duty_time';    -- 查询参数设置配置
		
	obclient> ALTER SYSTEM SET major_freeze_duty_time='03:00';  -- 修改合并配置
	obclient> ALTER SYSTEM SET major_freeze_duty_time='03:00';

-- 3. 修改成功后，可以通过 `SHOW PARAMETERS` 语句查看是否修改成功；
obclient>SHOW PARAMETERS LIKE 'major_freeze_duty_time';
```


```sql
-- 通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'minor_freeze_times';

-- 修改转储配置
ALTER SYSTEM SET minor_freeze_times=0;   -- 用于设置多少次小合并触发一次全局合并；【从 V4.0.0 版本开始弃用】
```

###### 2.1.1 设置轮转合并顺序
`在 V4.0.0 版本中取消了轮转合并`；

```sql
-- 设置轮转合并顺序
-- 假设集群中有三个zone，分别是z1,z2,z3，想设置轮转合并的顺序为"z1 -> z2 -> z3"，步骤如下：
alter system set enable_manual_merge = false;    -- 关闭手动合并
alter system set enable_merge_by_turn = true;    -- 开启轮转合并

-- 
alter system set zone_merge_order = 'z1,z2,z3';  -- 设置合并顺序
alter system set zone_merge_order = '';          -- 取消自定义合并顺序
```


##### 2.2 合并参数设置
转储最常用的控制参数是 *minor_freeze_times* ，该参数控制在转储多少次后自动转化为合并。如果该参数设置为 0，则表示关闭转储功能，每次租户 MemTable 内存的使用达到阈值不会触发转储而是直接进行合并，更多详细情况请查看文档：常用参数配置列表；

合并相关参数说明如下表所示：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍#1.6 转储，合并相关\|OceanBase 配置项，变量详细介绍#1.6 转储，合并相关]]，；
1. `enable_major_freeze`：集群级配置项，表示是否开启合并；默认值为 True，取值范围为：True，False；
	1. 
2. `major_freeze_duty_time`：租户级配置项，每天定时合并的时间；默认值为 02:00，取值范围为：`[00:00,24:00]`；
	1. 在 V4.0.0 版本中，该参数调整为【_租户级_】；
3. `minor_freeze_times`：用于设置多少次小合并触发一次全局合并；【*从 V4.0.0 版本开始弃用*】；
	
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02/ob/ocean-base/#d1c59a" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



16. `minor_freeze_times`：用于设置多少次小合并触发一次全局合并；【*从 V4.0.0 版本开始弃用*】； 

</div></div>

2. `major_compact_trigger`：用于设置多少次小合并触发一次全局合并；该参数控制在转储多少次后自动转化为合并；
	
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02/ob/ocean-base/#2b32eb" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



17. `major_compact_trigger`：用于设置多少次小合并触发一次全局合并；该参数控制在转储多少次后自动转化为合并； 

</div></div>

1. `default_progressive_merge_num`：租户级配置项，建表时默认的合并行为；默认值为 0；取值范围为：`[0, +∞)`  ；
	1. 0 ：表示使用默认值 100；
	2. 1：表示强制执行全量合并，不执行渐进合并；
	3. 大于 1 ：表示发生 Schema 变更时按照指定轮次做渐进合并；
	4. 说明：您也可以在表创建后，通过 `ALTER TABLE table_name SET PROGRESSIVE_MERGE_NUM=0;` 语句修改表的合并行为；
2. `merger_check_interval`：租户级配置项，每个 Zone 的合并进度检查间隔；默认值为 10m；取值范围：[10s, 60m]；
3. `_enable_adaptive_compaction`：租户级配置项，控制是否开启自适应合并；
4. *轮转合并配置项*
{ #3990a6}

	1. `enable_manual_merge`: OB 的配置项，指示是否开启手动合并；
	2. `enable_merge_by_turn`：OB 的配置项，指示是否开启自动轮转合并；
	
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02/ob/ocean-base/#579c89" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



20. `enable_merge_by_turn`：用于设置是否开启轮转合并策略；*从 V4.0.0 版本开始弃用*； 

</div></div>

	1. `merge_list` / `zone_merge_order`：指定自动轮转合并的合并顺序；
	
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02/ob/ocean-base/#f50cc7" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



22. `merge_list / zone_merge_order`：轮转合并开启时 Zone 的合并顺序；*从 V4.0.0 版本开始弃用*； 

</div></div>

5. `zone_merge_timeout`：；
6. data_disk_usage_limit_percentage：；
7. datafile_disk_percentage：；
8. `merge_thread_count`：用于设置每日合并工作的线程数；合并线程数；*从 V4.0.0 版本开始弃用*；

### 参考文档
