---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 发起日志备份/","dgPassFrontmatter":true}
---


#### 1 前提条件
必须完成备份前准备，[[15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 备份前准备\|OB 备份前准备]]；

#### 2 操作步骤

##### 2.1 配置备份模式，并开启归档日志压缩功能
该步骤为可选步骤，使用默认也是可以的；

备份模式目前支持 Optional 模式和 Mandatory 模式，如果不配置，默认为 Optional 模式：
-   Optional 模式表示以用户业务优先。在该模式下，当备份（日志归档）来不及的情况下，日志可能来不及备份就回收了，可能会发生备份断流；
    
-   Mandatory 模式表示以备份优先。在该模式下如果备份跟不上用户数据的写入，可能会导致用户无法写入；
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 2.配置备份模式，并开启归档日志压缩功能
obclient> ALTER SYSTEM SET backup_log_archive_option = 'optional compression= enable';
```

日志压缩目前支持的压缩算法有：zstd_1.3.8 和 lz4_1.0，默认使用压缩算法 lz4_1.0；
```sql
-- 可以根据业务需要动态修改压缩算法，您可以执行以下命令修改压缩算法
obclient> ALTER SYSTEM SET backup_log_archive_option='optional compression= zstd_1.3.8';
obclient> ALTER SYSTEM SET backup_log_archive_option='optional compression= lz4_1.0';
```

`配置后，如果需要关闭日志归档压缩:`
```sql
-- 对于当前备份模式为 Optional 模式的场景
ALTER SYSTEM SET backup_log_archive_option = 'compression= disable'; -- 命令直接关闭归档日志压缩功能，
ALTER SYSTEM SET backup_log_archive_option = 'mandatory compression= disable'; -- 配置备份模式并关闭归档日志压缩功能。
    
-- 对于当前备份模式为 Mandatory 模式的场景，
ALTER SYSTEM SET backup_log_archive_option = 'optional compression= disable'; --命令配置备份模式并关闭归档日志压缩
```

##### 2.2 启动日志备份
为了减少日志备份发起的耗时，建议在开启日志备份前进行一次转储，待转储完成后再备份。这是因为日志备份的起始位点是最近一次转储位点，转储以后可以减少日志备份启动的时间；启动成功后，OceanBase 数据库会自动将集群产生的事务日志定期备份到之前指定的备份目的地；

```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.启动，停止日志备份
obclient> SHOW PARAMETERS LIKE '%backup_dest%';    -- 查询日志备份相关配置项
obclient> alter system minor freeze;               -- 转储数据，建议在开启日志备份前进行一次转储，待转储完成后再备份；
obclient> ALTER SYSTEM ARCHIVELOG;                 -- 启动日志备份（若日志备份中断后，重新启动失败，可以先停止再启动）
	-- 若日志备份中断后，重新启动后，建议最后马上重新进行一次全量备份，因为中断后重新启动会导致之前的备份不可用；
obclient> ALTER SYSTEM NOARCHIVELOG;               -- 停止日志备份任务(使用 root 用户登录数据库的 sys 租户) 
```

##### 2.3 确认日志备份任务是否已开始

```sql
-- 查看日志的备份进度(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
/*
	当 `STATUS`为 `DOING`时，则表示日志备份任务已开始;
*/

-- 确认日志备份任务是否已开始
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG_SUMMARY;  -- 查看当前日志归档状态信息
/*
	log_archive_status: 日志归档状态的种类，主要有：STOP: 没有日志归档备份；BEGINNING: 正在启动日志归档备份；DOING: 正在日志归档备份；
		STOPPING: 停止日志归档备份；INTERRUPTED: 日志归档备份断流了；
*/
```
更多 `CDB_OB_BACKUP_ARCHIVELOG` 视图的说明，请参见 [CDB_OB_BACKUP_ARCHIVELOG](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945458)，；
除了通过 SQl 语句查询，也可通过 OCP 查询，详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；

##### 2.4 4.查看备份集中 Piece 的状态
```sql
obclient> SELECT * FROM CDB_OB_BACKUP_PIECE_FILES;
```
有关该系统视图中各个字段的详细解释，请参见 [CDB_OB_BACKUP_PIECE_FILES](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945464)，；


### 参考文档：
1. [查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；