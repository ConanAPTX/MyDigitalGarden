---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复/OB 发起数据备份/","dgPassFrontmatter":true}
---


#### 1 前提条件
1. 确认当前已发起日志备份，仅当日志备份任务的 `STATUS`为 `DOING` 时，才能开始数据备份；
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复/OB 发起日志备份\|OB 发起日志备份]]，；

#### 2 发起全量备份，增量备份

增量备份是从上一个全量备份开始，备份所有修改过的宏块。在执行增量备份前，请确保已经有全量备份存在；`增量备份会默认直接使用之前全量备份或者上一次增量备份的各类参数设置；`

##### 2.1 设置备份的密码
该密码是备份出来的备份集的密码。如果设置了该选项，在使用该备份集进行恢复时，需要输入该密码，且该密码不能被删除

```sql
-- 1. 使用 `root` 用户登录数据库的 `sys` 租户

-- 2. 设置备份的密码
-- 语法：SET ENCRYPTION ON IDENTIFIED BY 'password' ONLY;
obclient> SET ENCRYPTION ON IDENTIFIED BY '******' ONLY;     -- 设置备份的密码
	-- 设置备份密码后，在未发起数据备份前，您可以通过断开会话再重新连接数据库的方式取消本次设置；
```


##### 2.2 发起全量备份，增量备份
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.发起(停止)全量备份
	-- 执行全量备份前，对集群进行一次合并；
	ALTER SYSTEM MAJOR FREEZE; 

	ALTER SYSTEM BACKUP DATABASE;       -- 发起全量备份
	ALTER SYSTEM CANCEL BACKUP;         -- 停止数据备份任务(使用 root 用户登录数据库的 sys 租户)
		-- 停止数据备份任务后，如果再次发起数据备份，系统会重新开始备份；
```

```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.启动增量备份
	obclient> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;  -- 启动增量备份
```


```sql
-- 1.使用 root 用户登录数据库的 sys 租户

-- 2.发起租户级别的数据备份
	ALTER SYSTEM BACKUP TENANT tenant_name_list TO backup_destination;
	    -- tenant_name_list：表示需要备份的租户列表；
	    -- backup_destination：表示备份文件的目的端，目前支持 NFS 和 OSS 作为备份介质；
	
	-- 使用示例：
	ALTER SYSTEM BACKUP TENANT tenant1,tenant2 TO "file:///ob_backup/";
```


##### 2.3 停止数据备份任务
```sql
-- 1.使用 root 用户登录数据库的 sys 租户

-- 停止数据备份任务(使用 root 用户登录数据库的 sys 租户)
obclient> ALTER SYSTEM CANCEL BACKUP;
	-- 停止数据备份任务后，如果再次发起数据备份，系统会重新开始备份；
```




### 参考文档：
