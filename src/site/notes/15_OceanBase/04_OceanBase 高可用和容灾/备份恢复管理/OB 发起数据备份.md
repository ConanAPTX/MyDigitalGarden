---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 发起数据备份/","dgPassFrontmatter":true}
---


#### 1. 前提条件
1. 确认当前已发起日志备份，仅当日志备份任务的 `STATUS`为 `DOING` 时，才能开始数据备份；
	1. [[15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 发起日志备份\|OB 发起日志备份]]，；

#### 2. 发起全量备份
##### 1. 设置备份的密码
该密码是备份出来的备份集的密码。如果设置了该选项，在使用该备份集进行恢复时，需要输入该密码，且该密码不能被删除

```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 设置备份的密码
--语法：SET ENCRYPTION ON IDENTIFIED BY 'password' ONLY;
obclient> SET ENCRYPTION ON IDENTIFIED BY '******' ONLY;
```

> [!NOTE] 说明：
> 设置备份密码后，在未发起数据备份前，您可以通过断开会话再重新连接数据库的方式取消本次设置；

##### 2. 发起全量备份
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 发起全量备份
obclient> ALTER SYSTEM BACKUP DATABASE;
```

##### 3. 查看备份任务的状态和详细信息

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base///#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





### 查询数据备份进度及备份任务历史
#### 1 查询数据备份进度以及备份任务历史

```sql
-- 查看备份进度，即查询正在备份的任务(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_PROGRESS;

-- 查看备份历史(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS;
```
更多 `CDB_OB_BACKUP_PROGRESS` 视图的说明，请参见 [CDB_OB_BACKUP_PROGRESS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945440) ，；
更多 `CDB_OB_BACKUP_SET_DETAILS` 视图的说明，请参见  [CDB_OB_BACKUP_SET_DETAILS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945439)，；


#### 2 通过视图查看日志的备份进度
```sql
-- 查看日志的备份进度(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
/*
当 `STATUS`为 `DOING`时，则表示日志备份任务已开始;
*/
```
更多 `CDB_OB_BACKUP_ARCHIVELOG` 视图的说明，请参见 [CDB_OB_BACKUP_ARCHIVELOG](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945458)，；


#### 3 通过 OCP 查看备份进度
详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；

### 参考文档
1. [查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；


</div></div>


#### 3. 发起增量备份
增量备份是从上一个全量备份开始，备份所有修改过的宏块。在执行增量备份前，请确保已经有全量备份存在；

> [!NOTE] 说明：
> 增量备份会默认直接使用之前全量备份或者上一次增量备份的各类参数设置；

##### 1. 启动增量备份
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户
obclient> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;  -- 启动增量备份
```


##### 2. 查看增量备份的进展

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base///#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





### 查询数据备份进度及备份任务历史
#### 1 查询数据备份进度以及备份任务历史

```sql
-- 查看备份进度，即查询正在备份的任务(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_PROGRESS;

-- 查看备份历史(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS;
```
更多 `CDB_OB_BACKUP_PROGRESS` 视图的说明，请参见 [CDB_OB_BACKUP_PROGRESS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945440) ，；
更多 `CDB_OB_BACKUP_SET_DETAILS` 视图的说明，请参见  [CDB_OB_BACKUP_SET_DETAILS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945439)，；


#### 2 通过视图查看日志的备份进度
```sql
-- 查看日志的备份进度(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
/*
当 `STATUS`为 `DOING`时，则表示日志备份任务已开始;
*/
```
更多 `CDB_OB_BACKUP_ARCHIVELOG` 视图的说明，请参见 [CDB_OB_BACKUP_ARCHIVELOG](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945458)，；


#### 3 通过 OCP 查看备份进度
详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；

### 参考文档
1. [查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；


</div></div>
