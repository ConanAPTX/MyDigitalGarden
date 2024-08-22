---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v3.x/查询 OceanBase 备份信息_V2.x，V3.x/","dgPassFrontmatter":true}
---



### 查询 OceanBase 备份信息_V2.x，V3.x
查看备份任务的状态和详细信息

详细情况：[[15_OceanBase/99_OceanBase 内部表介绍#1.1 字典视图_备份恢复相关\|99_OceanBase 内部表介绍#1.1 字典视图_备份恢复相关]]

#### 1 查询数据备份信息

`CDB_OB_BACKUP_PROGRESS`：展示数据库备份任务的进度；`CDB_OB_BACKUP_SET_DETAILS`：展示备份集的详细信息；`CDB_OB_BACKUP_SET_FILES`：展示备份集的文件状态；用于展示备份集的文件状态；关于更多详情：[[15_OceanBase/99_OceanBase 内部表介绍\|99_OceanBase 内部表介绍]]，；

除了通过 SQl 语句查询，也可通过 OCP 查询，详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；

##### 1.1 查看数据备份进度
```sql
-- 1.使用 root 用户登录数据库的 sys 租户；

-- 2.查看备份进度，即查询正在备份的任务；
USE oceanbase;
SELECT * FROM oceanbase.CDB_OB_BACKUP_PROGRESS;    -- 若备份已完成，则查询无结果；
	-- BACKUP_TYPE：备份类型；D，全量备份；I，增量备份；
	-- STATUS：备份状态；
```
[[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_1000#1.4 字段说明\|系统视图_日志备份相关_1000#1.4 字段说明]]


##### 1.2 查看备份历史
```sql
-- 4.查看备份历史(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS;
SELECT incarnation, tenant_id, bs_key , backup_type, encryption_mode, start_time, completion_time, elapsed_secondes , keep, output_bytes_display, output_rate_bytes_display, status 
FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS
order by start_time desc limit 10;
```

##### 1.3 查询备份任务历史
```sql
-- 3.查看备份任务的历史；
SELECT * FROM oceanbase.CDB_OB_BACKUP_SET_FILES;
```


#### 2 日志备份信息
##### 2.1 查看日志的备份进度
`oceanbase.CDB_OB_BACKUP_ARCHIVELOG`：用于展示正在备份的日志状态；该表 1 个租户 1 条记录；关于更多详情：[[15_OceanBase/99_OceanBase 内部表介绍\|99_OceanBase 内部表介绍]]，；

除了通过 SQl 语句查询，也可通过 OCP 查询，详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；

```sql
-- 1.使用 root 用户登录数据库的 sys 租户；

-- 查看日志的备份进度(使用 root 用户登录数据库的 sys 租户)
USE oceanbase;
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
SELECT INCARNATION,LOG_ARCHIVE_ROUND,TENANT_ID,STATUS,START_PIECE_ID,BACKUP_PIECE_ID, MIN_FIRST_TIME, MAX_NEXT_TIME
	/*,INPUT_BYTES, OUTPUT_BYTES, COMPRESSION_RATIO, INPUT_BYTES_DISPLAY, OUTPUT_BYTES_DISPLAY*/
FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
+-------------+-------------------+-----------+--------+----------------+-----------------+----------------------------+----------------------------+-------------+--------------+-------------------+---------------------+----------------------+
| INCARNATION | LOG_ARCHIVE_ROUND | TENANT_ID | STATUS | START_PIECE_ID | BACKUP_PIECE_ID | MIN_FIRST_TIME             | MAX_NEXT_TIME              | INPUT_BYTES | OUTPUT_BYTES | COMPRESSION_RATIO | INPUT_BYTES_DISPLAY | OUTPUT_BYTES_DISPLAY |
+-------------+-------------------+-----------+--------+----------------+-----------------+----------------------------+----------------------------+-------------+--------------+-------------------+---------------------+----------------------+
|           1 |                 1 |         1 | DOING  |              1 |               1 | 2021-07-22 15:18:06.135913 | 2021-07-22 15:18:10.116704 |           0 |            0 |              NULL | 0.00MB              | 0.00MB               |
|           1 |                 1 |      1001 | DOING  |              1 |               1 | 2021-07-22 15:18:06.135913 | 2021-07-22 15:18:10.483601 |           0 |            0 |              NULL | 0.00MB              | 0.00MB               |
|           1 |                 1 |      1002 | DOING  |              1 |               1 | 2021-07-22 15:18:06.135913 | 2021-07-22 15:18:10.116704 |           0 |            0 |              NULL | 0.00MB              | 0.00MB               |
+-------------+-------------------+-----------+--------+----------------+-----------------+----------------------------+----------------------------+-------------+--------------+-------------------+---------------------+----------------------+
3 rows in set
/*
	当 `STATUS`为 `DOING`时，则表示日志备份任务已开始;
*/
```
[[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_010#1.2 字段说明\|系统视图_日志备份相关_010#1.2 字段说明]]


##### 2.2 查询日志备份的任务信息
`oceanbase.CDB_OB_BACKUP_ARCHIVELOG_SUMMARY`：用于展示日志备份的任务信息；该视图更多信息：[[15_OceanBase/99_OceanBase 内部表介绍\|99_OceanBase 内部表介绍]]，： 

```sql
-- 确认日志备份任务是否已开始
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG_SUMMARY;  -- 查看当前日志归档状态信息
/*
	log_archive_status: 日志归档状态的种类，主要有：【STOP】: 没有日志归档备份；【BEGINNING】: 正在启动日志归档备份；
		【DOING】: 正在日志归档备份；【STOPPING】: 停止日志归档备份；【INTERRUPTED】: 日志归档备份断流了；
*/
```
[[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_010#2.2 字段说明\|系统视图_日志备份相关_010#2.2 字段说明]]


##### 2.3 查看备份集中 Piece 的状态
`oceanbase.CDB_OB_BACKUP_PIECE_FILES`：用于展示备份集中 Piece 的状态；该表 1 个租户 1 个日志分片一条记录 ；该视图更多信息：[[15_OceanBase/99_OceanBase 内部表介绍\|99_OceanBase 内部表介绍]]，；

```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户;

-- 3.查看日志分片情况
SELECT * FROM oceanbase.CDB_OB_BACKUP_PIECE_FILES;
SELECT INCARNATION,TENANT_ID,ROUND_ID,BACKUP_PIECE_ID,COPY_ID,CREATE_DATE,START_TS,CHECKPOINT_TS,MAX_TS,STATUS,FILE_STATUS,COMPATIBLE,START_PIECE_ID
FROM oceanbase.CDB_OB_BACKUP_PIECE_FILES
where TENANT_ID = 1001
order by TENANT_ID, START_TS;
+-------------+-----------+----------+-----------------+---------+-------------+----------------------------+----------------------------+----------------------------+--------+-------------+------------+----------------+
| INCARNATION | TENANT_ID | ROUND_ID | BACKUP_PIECE_ID | COPY_ID | CREATE_DATE | START_TS                   | CHECKPOINT_TS              | MAX_TS                     | STATUS | FILE_STATUS | COMPATIBLE | START_PIECE_ID |
+-------------+-----------+----------+-----------------+---------+-------------+----------------------------+----------------------------+----------------------------+--------+-------------+------------+----------------+
|           1 |         1 |        1 |               1 |       0 |    20210430 | 2021-04-30 00:00:28.197301 | 2021-04-30 00:59:49.402568 | 2021-04-30 01:02:32.336195 | FROZEN | AVAILABLE   |          1 |              1 |
|           1 |         1 |        1 |               2 |       0 |    20210430 | 2021-04-30 01:02:32.336195 | 2021-04-30 02:01:38.260158 | 2021-04-30 02:04:10.990039 | ACTIVE | AVAILABLE   |          1 |              1 |
|           1 |      1001 |        1 |               1 |       0 |    20210430 | 2021-04-30 00:00:28.197301 | 2021-04-30 01:00:00.733364 | 2021-04-30 01:02:32.166782 | FROZEN | AVAILABLE   |          1 |              1 |
|           1 |      1001 |        1 |               2 |       0 |    20210430 | 2021-04-30 01:02:32.166782 | 2021-04-30 02:01:38.260158 | 2021-04-30 02:04:09.731651 | ACTIVE | AVAILABLE   |          1 |              1 |
+-------------+-----------+----------+-----------------+---------+-------------+----------------------------+----------------------------+----------------------------+--------+-------------+------------+----------------+
4 rows in set
/*
STATUS：切 Piece 的状态；
	【ACTIVE】：当前活跃的 Piece；【FREEZING】：正在冻结的 Piece；【FROZEN】：已经冻结的 Piece，此状态后，该 Piece 的状态不会再修改了；
FILE_STATUS：备份日志分片的文件状态；
	【AVAILABLE】：表示有效的备份，可以用于恢复；
	【COPYING】：表示日志文件正在备份中；
	【INCOMPELTE】：表示日志文件仅拷贝了部分，仅限于定期备份备份日志的场景。该 Copy 的 checkpoint_ts 比原备份的 checkpoint ts 小，可以用于 checkpoint_ts 之前的恢复；
	【DELETING】：日志文件正在删除；
	【EXPIRED】: 表示备份文件已失效；
	【BROKEN】：备份的文件不完整，不可使用；
	【DELETED】：日志文件已删除；
*/
```
[[15_OceanBase/99_内部表介绍/系统视图_日志备份相关_999#1.3 字段说明\|系统视图_日志备份相关_999#1.3 字段说明]]


### 参考文档
1. *查看备份进度*：[V2.2.77](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000377196)，[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；


