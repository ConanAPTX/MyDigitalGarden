---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/OceanBase 数据库合并超时问题排查方法/","dgPassFrontmatter":true}
---


### OceanBase 数据库合并超时问题排查方法
#### 1 适用版本

OceanBase 数据库所有版本

#### 2 排查步骤
##### 2.1 确认合并配置项
OceanBase 数据库中包含以下合并相关的配置项：可以参考官方文档；


##### 2.2 确认合并状态
本例中，当前的 `frozen_version` 为 `25`，表示集群需要合并到 `25` 版本，但 `cn-shanghai-e` 这个 Zone 副本只合并到了 `24` 版本；

```sql
-- 
obclient> select * from __all_zone where name = "frozen_version" or name = "last_merged_version";
+----------------------------+----------------------------+---------------+---------------------+-------+------+
| gmt_create                 | gmt_modified               | zone          | name                | value | info |
+----------------------------+----------------------------+---------------+---------------------+-------+------+
| 2020-12-07 16:01:30.793286 | 2020-12-30 02:00:00.445594 |               | frozen_version      |    25 |      |
| 2020-12-07 16:01:30.793490 | 2020-12-29 02:01:12.449651 |               | last_merged_version |    24 |      |
| 2020-12-07 16:01:30.794375 | 2020-12-29 02:01:11.880114 | cn-shanghai-e | last_merged_version |    24 |      |
| 2020-12-07 16:01:30.795109 | 2020-12-30 02:01:15.563291 | cn-shanghai-f | last_merged_version |    25 |      |
| 2020-12-07 16:01:30.795842 | 2020-12-30 02:01:22.694016 | cn-shanghai-g | last_merged_version |    25 |      |
+----------------------------+----------------------------+---------------+---------------------+-------+------+
5 rows in set (0.00 sec)

-- 继续查询 `__all_zone` 表，查看 `cn-shanghai-e` 的合并状态，发现状态为 `MERGING`，表示正在合并；
obclient> select * from __all_zone where name = "merge_status";
+----------------------------+----------------------------+---------------+---------------------+---------+------+
| gmt_create                 | gmt_modified               | zone          | name                | value   | info |
+----------------------------+----------------------------+---------------+---------------------+---------+------+
| 2020-12-07 16:01:30.793286 | 2020-12-30 02:00:00.445594 |               | merge_status        | MERGING |      |
| 2020-12-07 16:01:30.794375 | 2020-12-29 02:01:11.880114 | cn-shanghai-e | merge_status        | MERGING |      |
| 2020-12-07 16:01:30.795109 | 2020-12-30 02:01:15.563291 | cn-shanghai-f | merge_status        |   IDLE  |      |
| 2020-12-07 16:01:30.795842 | 2020-12-30 02:01:22.694016 | cn-shanghai-g | merge_status        |   IDLE  |      |
+----------------------------+----------------------------+---------------+---------------------+---------+------+
5 rows in set (0.00 sec)
```

根据上面查询到的信息，可以将问题归于【`未合并问题`】、【`合并超时问题`】、【`合并慢问题`】，并做具体的分析；

```sql
ZONE未合并：
SELECT * FROM __all_zone WHERE name = "global_broadcast_version" or name = "broadcast_version"; 
如果 broadcast_version 等于 last_merged_version，且 last_merged_version 落后于 global_broadcast_version，说明 RootService 没有发起相关 Zone 的合并；

副本未合并：
-- 查询哪个主机上的副本为合并（假设当前要合并的目标版本是25）
SELECT * FROM __all_virtual_meta_table WHERE data_version!= 25 LIMIT 10; 
```

合并超时问题
```
定位未合并到指定版本的partition

n判断该partition 的合并任务是否在执行中：SELECT * FROM __all_virtual_sys_task_status;

n如果该partition 没有调度合并任务，判断该partition 的最新的转储sstable的snapshot_version有没有推过 freeze_info 点，如果没有推过freeze_info点，说明转储有问题
```
合并慢问题

```sql
通过
SELECT /*+ query_timeout(10000000)*/* FROM __all_virtual_partition_sstable_merge_info
WHERE merge_type= “major merge” and version = “<merge_version>” 
ORDER BY merge_cost_timedesc limit 5;

语句找到合并耗时最多的几个partition 来具体分析

n通过表 __all_virtual_partition_sstable_merge_info 查看宏块重用情况

n查看合并开始时间是否合理
```


##### 2.3 排查切主相关问题
根据上面的信息，发现正在合并，此时查询该 Zone 的 `global_broadcast_version` 与 `broadcast_version`；如果 `broadcast_version` 等于 `last_merged_version`，且 `last_merged_version` 落后于 `global_broadcast_version`，说明 RootService 没有发起相关 Zone 的合并；

```sql
obclient> select * from __all_zone where name = "global_broadcast_version" or name = "broadcast_version";
+----------------------------+----------------------------+---------------+--------------------------+---------+------+
| gmt_create                 | gmt_modified               | zone          | name                     | value   | info |
+----------------------------+----------------------------+---------------+--------------------------+---------+------+
| 2020-12-07 16:01:30.793286 | 2020-12-30 02:00:00.445594 |               | global_broadcast_version |   25    |      |
| 2020-12-07 16:01:30.794375 | 2020-12-29 02:01:11.880114 | cn-shanghai-e | broadcast_version        |   24    |      |
| 2020-12-07 16:01:30.795109 | 2020-12-30 02:01:15.563291 | cn-shanghai-f | broadcast_version        |   25    |      |
| 2020-12-07 16:01:30.795842 | 2020-12-30 02:01:22.694016 | cn-shanghai-g | broadcast_version        |   25    |      |
+----------------------------+----------------------------+---------------+--------------------------+---------+------+
5 rows in set (0.00 sec)
```
    
对于 Root Service 未发起相关 Zone 合并的情况，按以下方法排查。

1.  首先排查 RootService 合并调度线程是否有报错。
	
	如果有返回值，则说明合并调度线程存在错误。

```shell
grep "daily.*merge.*ret=-" rootservice.log
```
	
2.  然后检查集群是否正在补副本。
	
	如果以下查询有返回值，则说明正在进行副本的负载均衡。
	
```shell
obclient> select count(*) from __all_virtual_replica_task; 
```
        
##### 2.4 确认未合并副本信息
本例继续假设当前要合并的目标版本是 `25`，查询 meta 表查看 `data_version != 25` 的副本以缩小排查范围。
    
-   对于 OceanBase 数据库 V1.X 版本：
查询 `__all_virtual_core_meta_table`、`__all_virtual_core_root_table`、`__all_root_table` 与 `__all_meta_table` 表；

```sql

```




-   对于 OceanBase 数据库 V2.X 及后续版本：
查询 `__all_virtual_core_meta_table`、`__all_virtual_core_root_table`、`__all_root_table` 和 `__all_virtual_meta_table` 表；  

```shell
obclient> select * from __all_virtual_meta_table where data_version != 25 limit 10;
+-----------+------------------+--------------+---------------+----------+----------------------------+----------------------------+----------+---------+---------------+---------------+------+-----------------------------------------------------------------------------------------------------------+-----------+-----------+--------------+---------------+--------------+-----------------+--------------------+--------------------+-------------+---------+--------------+---------------+-----------------------+------------+--------------------+--------+-----------+--------------------+------------------+
| tenant_id | table_id         | partition_id | svr_ip        | svr_port | gmt_create                 | gmt_modified               | sql_port | unit_id | partition_cnt | zone          | role | member_list                                                                                               | row_count | data_size | data_version | data_checksum | row_checksum | column_checksum | is_original_leader | is_previous_leader | create_time | rebuild | replica_type | required_size | status                | is_restore | partition_checksum | quorum | fail_list | recovery_timestamp | memstore_percent |
+-----------+------------------+--------------+---------------+----------+----------------------------+----------------------------+----------+---------+---------------+---------------+------+-----------------------------------------------------------------------------------------------------------+-----------+-----------+--------------+---------------+--------------+-----------------+--------------------+--------------------+-------------+---------+--------------+---------------+-----------------------+------------+--------------------+--------+-----------+--------------------+------------------+
|      1001 | 1100611139463766 |            0 | xxx.xxx.x.xa |     xxxx | 2020-12-29 10:34:15.176561 | 2020-12-29 10:34:15.205753 |     2881 |    1001 |             0 | cn-shanghai-e |    1 | xxx.xxx.x.xx:2882:1609209255175464,xxx.xxx.x.xx:2882:1609209255175464,xxx.xxx.x.xx:2882:1609209255175464 |         0 |         0 |           24 |             0 |            0 |                 |                  0 |   1609209255204831 |           0 |       0 |            0 |             0 | REPLICA_STATUS_NORMAL |          0 |                  0 |      3 |           |                  0 |              100 |
+-----------+------------------+--------------+---------------+----------+----------------------------+----------------------------+----------+---------+---------------+---------------+------+-----------------------------------------------------------------------------------------------------------+-----------+-----------+--------------+---------------+--------------+-----------------+--------------------+--------------------+-------------+---------+--------------+---------------+-----------------------+------------+--------------------+--------+-----------+--------------------+------------------+
1 row in set (0.07 sec)
```

根据上面的信息，发现是 pkey 为`{tid:1100611139463766, partition_id:0}` 的表在 `xxx.xxx.x.xa:xxxx` 机器上未合并。
    
也可以通过搜索 `rootservice.log` 定位是哪个表阻塞了合并。

### 参考文档
1. [OceanBase 数据库合并超时问题排查方法](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001025)，；


