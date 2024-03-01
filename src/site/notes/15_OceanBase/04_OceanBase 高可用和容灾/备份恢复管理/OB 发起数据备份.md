---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 发起数据备份/","dgPassFrontmatter":true}
---


#### 1 前提条件
1. 确认当前已发起日志备份，仅当日志备份任务的 `STATUS`为 `DOING` 时，才能开始数据备份；
	1. [[15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 发起日志备份\|OB 发起日志备份]]，；

#### 2 发起全量备份
##### 2.1 设置备份的密码
该密码是备份出来的备份集的密码。如果设置了该选项，在使用该备份集进行恢复时，需要输入该密码，且该密码不能被删除

```sql
-- 1. 使用 `root` 用户登录数据库的 `sys` 租户

-- 2. 设置备份的密码
-- 语法：SET ENCRYPTION ON IDENTIFIED BY 'password' ONLY;
obclient> SET ENCRYPTION ON IDENTIFIED BY '******' ONLY;     -- 设置备份的密码
	-- 设置备份密码后，在未发起数据备份前，您可以通过断开会话再重新连接数据库的方式取消本次设置；
```

##### 2.2 发起全量备份

```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.发起(停止)全量备份
obclient> ALTER SYSTEM BACKUP DATABASE;       -- 发起全量备份
obclient> ALTER SYSTEM CANCEL BACKUP;         -- 停止数据备份任务(使用 root 用户登录数据库的 sys 租户)
	-- 停止数据备份任务后，如果再次发起数据备份，系统会重新开始备份；
```

##### 2.3 查看备份任务的状态和详细信息

```sql
-- 查看备份进度，即查询正在备份的任务(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_PROGRESS;    -- 若备份已完成，则查询无结果；
	-- BACKUP_TYPE：备份类型；D，全量备份；I，增量备份；
	-- STATUS：备份状态；

-- 查看备份历史(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS;
SELECT incarnation, tenant_id, bs_key , backup_type, encryption_mode, start_time, completion_time, elapsed_secondes , keep, output_bytes_display, output_rate_bytes_display, status 
FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS
order by start_time desc limit 10;
```
更多 `CDB_OB_BACKUP_PROGRESS` 视图的说明，请参见 [CDB_OB_BACKUP_PROGRESS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945440) ，；
更多 `CDB_OB_BACKUP_SET_DETAILS` 视图的说明，请参见  [CDB_OB_BACKUP_SET_DETAILS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945439)，；
除了通过 SQl 语句查询，也可通过 OCP 查询，详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；


#### 3 发起增量备份
增量备份是从上一个全量备份开始，备份所有修改过的宏块。在执行增量备份前，请确保已经有全量备份存在；`增量备份会默认直接使用之前全量备份或者上一次增量备份的各类参数设置；`

```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.启动增量备份
obclient> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;  -- 启动增量备份
```


##### 3.1 查看增量备份的进展


### 参考文档：
1. [查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；