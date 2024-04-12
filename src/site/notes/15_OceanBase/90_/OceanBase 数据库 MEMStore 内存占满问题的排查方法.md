---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/90_/OceanBase 数据库 MEMStore 内存占满问题的排查方法/","dgPassFrontmatter":true}
---


### OceanBase 数据库 MEMStore 内存占满问题的排查方法
#### 1 适用版本
OceanBase 数据库 V2.X 版本；

#### 2 MEMStore 内存释放流程
整体来看 MEMStore 内存释放流程分为三步，一是活动的 MEMStore 要执行冻结，二是将冻结的 MEMStore 转储成 SSTable，最后释放转储完成的 MEMStore；因此，如果发现出现内存满的情况，首先按照这三步流程判断问题的大致范围；

MEMStore 使用内存的基本情况可以通过 *\_\_all_virtual_tenant_memstore_info* 表查询，重点关注 *active_memstore_used*、*total_memstore_used*、*major_freeze_trigger* 与 *memstore_limit* 四个字段；

分区在正常写入过程中，主备机的 MEMStore 会不断增长，MEMStore 能够使用的内存上限称为 *memstore_limit*，如果 MEMStore 占用的空间 *total_memstore_used* 增长到 *memstore_limit*，则会出现无法继续写入的情况。因此，当 *active_memstore_used* 使用超过 *major_freeze_trigger* 时，会触发转储，冻结当前的活跃的 MEMStore，并逐步将冻结的 MEMStore 转储到磁盘上最终释放内存；

```SQL
obclient> SELECT * FROM __all_virtual_tenant_memstore_info;
```


#### 3 排查方法
##### 3.1 检查冻结情况
首先查询 *\_\_all_virtual_tenant_memstore_info* 表的 *active_memstore_used* 列确定冻结是否完成；

```sql
-- 1.检查冻结情况
	-- 查询 __all_virtual_tenant_memstore_info 表的 active_memstore_used 列确定冻结是否完成；
	SELECT * FROM __all_virtual_tenant_memstore_info;
		-- 如果 active_memstore_used 的值超过 major_freeze_trigger，则说明冻结没有正常执行，需要根据以下步骤进一步检查；


-- 2.确认冻结触发线程
	如果冻结没有正常执行，首先检查触发冻结的线程是否正常工作，搜索关键日志 `====== tenant manager timer task ======` 是否周期性打印；
	如果上述日志没有周期性打印，则说明线程卡住，需要搜索线程日志或 pstack 查看超时的原因；
	
	[admin@hostname log]$ grep "tenant manager timer task" observer.log

-- 3.确认冻结失败的原因
	线程正常的情况下说明是有个别分区无法冻结，这种情况可以通过以下 SQL 查询；
	查看没有冻结的 MEMStore，按照 pkey 查找日志继续定位问题，也可以直接搜索冻结失败的日志；
	SELECT * 
	FROM __all_virtual_tenant_memstore_allocator_info 
	WHERE svr_ip=xxx AND tenant_id=xxx AND mt_is_frozen=0 
	ORDER BY mt_protection_clock;
```


##### 3.2 检查转储情况
如果上述步骤中， *total_memstore_used* 的值大于 *major_freeze_trigger* 的值，且 *active_memstore_used* 的值小于 *major_freeze_trigger* 的值，则说明有冻结的 MEMStore 没有释放，需要先确认冻结的 MEMStore 是否完成了转储；

###### 3.2.1 确定未释放的 MEMStore； 

```sql
-- 1.通过以下 SQL 检查未释放的 MEMStore 的 pkey；
SELECT * FROM __all_virtual_tenant_memstore_allocator_info 
WHERE svr_ip=xxx AND tenant_id=xxx 
ORDER BY mt_protection_clock
```

通过查询到 pkey 查询 `__all_virtual_table_mgr`，继续分析有 MEMStore 不释放的原因；

```sql
-- 2.可以通过如下查询快速定位未释放的 MEMTable；
-- 2.1 查看有活跃事务或者引用计数不为 `0` 或者备机读时间戳小于快照点的 MEMTable；
SELECT * FROM (
	SELECT A.svr_ip, A.table_id, A.partition_id, A.is_active, A.table_type, A.ref, A.write_ref, A.trx_count,  A.base_version, A.multi_version_start, A.snapshot_version, B.min_log_service_ts, B.min_trans_service_ts, B.min_replay_engine_ts 
	FROM (SELECT * FROM __all_virtual_table_mgr WHERE table_type=0 AND is_active =0 ) AS A 
	join (SELECT * FROM __all_virtual_partition_info WHERE svr_ip="xx.xx.xxx.xxx") AS B ON a.table_id = b.table_id AND a.partition_id=b.partition_idx AND a.svr_ip =b.svr_ip
) AS C 
WHERE c.write_ref >0 OR c.trx_count >0 OR (C.snapshot_version > least(least(min_trans_service_ts, min_replay_engine_ts), min_log_service_ts));

-- 2.2 查看上一查询得到的 MEMTable 对应的分布区域的日志回收进度，确定是否存在日志回放不及时；
SELECT * FROM  __all_virtual_partition_replay_status 
WHERE (table_id, partition_idx)  IN (
	SELECT table_id, partition_id FROM (
		SELECT A.svr_ip, A.table_id, A.partition_id, A.is_active, A.table_type, A.ref, A.write_ref, A.trx_count,  A.base_version, A.multi_version_start, A.snapshot_version, B.min_log_service_ts, B.min_trans_service_ts, B.min_replay_engine_ts 
		FROM (SELECT * FROM __all_virtual_table_mgr WHERE table_type=0 AND is_active =0 ) AS A 
		JOIN (SELECT * FROM __all_virtual_partition_info WHERE svr_ip="xxx.xxx.xx.xx") AS B ON a.table_id = b.table_id AND a.partition_id=b.partition_idx AND a.svr_ip =b.svr_ip
	) AS C 
	WHERE c.write_ref >0 or c.trx_count >0 OR (C.snapshot_version > least(least(min_trans_service_ts, min_replay_engine_ts), min_log_service_ts))
) AND svr_ip="XXX";
```


###### 3.2.2 确认转储调度

> [!NOTE] 如果是 MemStore 没有任何对应的 SSTable 生成，则有可能是 MEMStore 没有满足调度条件：
> 1. 备集群读时间戳落后 MEMStore 的 *snapshot_version*；
> 2. MEMStore 上仍有活跃事务（*trx_count* 不为零）；    

```sql
-- 其中，MEMStore 的 `snapshot_version` 与 `trx_count` 可以从 `__all_virtual_table_mgr` 中获得；
obclient> SELECT * FROM __all_virtual_table_mgr;
```
        
此外，备机读时间戳落后的原因常见的有无主或 rebuild 调度慢或执行失败，未结束的事务可以通过查询 `__all_virtual_trans_stat` 表，并通过日志进一步分析。备机读时间戳为`___all_virtual_partition_info` 中 `min_log_service_ts`、`min_trans_service_ts` 与 `min_replay_engine_ts` 三者中的最小值。

```unknow
obclient> SELECT * FROM ___all_virtual_partition_info;
```

###### 3.2.3 确认转储过程
如果调度转储条件已经满足，则说明转储本身没有执行或执行失败，可以通过转储的关键日志确认执行流程；

```unknow
add dag success.*pkey
task start process.*pkey
task finish process.*pkey
dag finish.*pkey
```


##### 3.3 检查 MEMStore 释放情况
当 MEMStore 对包含的主表和索引表全部转储完成后，可以通过搜索关键日志 `will release memtable.*pkey`，如果确认存在这条日志，则问题出在 MEMStore 最后的释放阶段，可以通过查询`__all_virtual_table_mgr` 的 `ref` 列确认未释放 MEMStore 的引用计数是否发生泄漏；

```sql
SELECT table_id, partition_id, base_version, snapshot_version 
FROM __all_virtual_table_mgr 
WHERE svr_ip='xxx' AND table_type=0 except SELECT table_id, partition_idx, base_version, snapshot_version FROM __all_virtual_memstore_info WHERE svr_ip='xxx';
```




### 参考文档
1. [OceanBase 数据库 MEMStore 内存占满问题的排查方法](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000120?back=kb)，；


