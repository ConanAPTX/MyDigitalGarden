---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 发起数据备份_V4.x/","dgPassFrontmatter":true}
---



## 1 OceanBase 发起数据备份_V4.x
### 1 发起全量数据备份
日志归档开始后，您可以发起数据备份；

#### 1.1 前提条件
在执行全量数据备份前，您需要进行以下事项：
1. 确认当前已开启归档模式，仅当日志归档任务的 `STATUS` 为 `DOING` 时，才能开始数据备份；
	1. 查看日志归档任务状态的相关操作请参见 [查看归档进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218396)，；
	2. 开启归档模式的相关操作请参见 [开启归档模式](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218401)，；

#### 1.2 备份准备（可选）
发起数据备份前，您可以为备份后的备份集设置密码；

> [!NOTE] `说明`：
> 该密码为备份后的备份集的密码。如果设置了该选项，在使用该备份集进行恢复时，需要输入该密码，且该密码不能被删除；

```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；

-- 2.（可选）执行以下命令，设置备份的密码
	obclient [(none)]> SET ENCRYPTION ON IDENTIFIED BY 'password' ONLY;
	
	-- 使用示例：
	obclient [(none)]> SET ENCRYPTION ON IDENTIFIED BY '******' ONLY;

```

> [!NOTE] `说明`：
> 设置备份密码后，在未发起数据备份前，您可以通过断开会话再重新连接数据库的方式取消本次设置；


#### 1.3 （可选）设置租户的备份并发度
1. `ha_low_thread_score`：用于设置高可用高优先级线程的当前工作线程数；*该配置项从 V4.0.0 版本开始引入*；

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02/ob/ocean-base/#47a560" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



9. `ha_low_thread_score`：用于设置高可用低优先级线程的当前工作线程数；*该配置项从 V4.0.0 版本开始引入*； 

</div></div>



````tab
tab: sys 租户
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2.（可选）设置租户的备份并发度
	-- 设置集群中所有租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all_user;
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all;

	-- 设置集群中指定租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = mysql_tenant;
```

> [!NOTE] `说明`：
> OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；


tab: 业务租户
```sql
-- 1. 租户管理员登录到数据库;
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.（可选）设置备份并发度；
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10;
```
````


#### 1.4 发起全量备份
完成准备工作后，您可以通过以下方式开启全量数据备份；

假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`，且租户 `mysql_tenant` 和 `oracle_tenant` 均已完成发起数据备份前的准备工作；

##### 1.4.1 系统租户发起全量备份
`sys` 租户可以对集群中的所有租户或指定租户发起全量数据备份；

（可选）设置租户的备份并发度：[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 发起数据备份_V4.x#1.3 （可选）设置租户的备份并发度\|OceanBase 发起数据备份_V4.x#1.3 （可选）设置租户的备份并发度]]，；

```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 3. 执行以下语句，发起全量数据备份；
	-- 3.1 对集群中的所有租户发起全量数据备份
		ALTER SYSTEM BACKUP DATABASE [PLUS ARCHIVELOG];   -- 该方式会对集群中的所有租户发起全量数据备份；
	
		-- 对集群中的所有租户发起全量数据备份的示例如下：
		ALTER SYSTEM BACKUP DATABASE;
			-- 命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 和 `oracle_tenant` 租户发起全量数据备份；
```

其中，`PLUS ARCHIVELOG` 为可选；添加 `PLUS ARCHIVELOG` 后，可以在数据备份过程中同时将归档日志一起备份，最终备份目录下会生成一份带有归档日志的完整数据集。该数据集具备恢复能力，您可以不依赖归档日志，使用该数据集将租户的数据恢复到 `min_restore_scn` 位点；

此外，对于社区版用户和单机版用户，可以通过该数据集创建备租户，有关如何使用带归档日志的完整数据集创建备租户的详细操作，请参见 [使用 BACKUP DATABASE PLUS ARCHIVELOG 功能创建备租户](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218767)，；

```sql
	-- 3.2 对集群中的指定租户发起全量数据备份
		-- 该方式仅对指定租户发起全量数据备份，不会影响集群中的其他租户。语句如下：
		ALTER SYSTEM BACKUP TENANT [=] mysql_tenant [PLUS ARCHIVELOG];
	
		-- 对 `mysql_tenant` 租户发起全量数据备份的示例如下：
		obclient [(none)]> ALTER SYSTEM BACKUP TENANT = mysql_tenant; 
```


> [!NOTE] `说明`：
> 同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；

命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 租户发起全量数据备份；
全量备份过程中，可以实时查看全量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


##### 1.4.2 用户租户发起全量备份
用户租户可以对本租户发起全量数据备份，不会影响其他租户；

```sql
-- 1. 租户管理员登录到数据库;
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 3. 执行以下语句，发起全量数据备份
	ALTER SYSTEM BACKUP DATABASE [PLUS ARCHIVELOG];

	-- 用户租户对本租户发起全量数据备份的示例如下：
	obclient [(none)]> ALTER SYSTEM BACKUP DATABASE;
```
其中，`PLUS ARCHIVELOG` 为可选。添加 `PLUS ARCHIVELOG` 后，可以在数据备份过程中同时将归档日志一起备份，最终备份目录下会生成一份带有归档日志的完整数据集。该数据集具备恢复能力，您可以不依赖归档日志，使用该数据集将租户的数据恢复到 `min_restore_scn` 位点；

此外，对于社区版用户和单机版用户，可以通过该数据集创建备租户，有关如何使用带归档日志的完整数据集创建备租户的详细操作，请参见 [使用 BACKUP DATABASE PLUS ARCHIVELOG 功能创建备租户](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218767)，；

命令执行成功后，系统会对本租户发起全量数据备份；

全量备份过程中，可以实时查看全量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


### 2 发起增量数据备份
增量备份是从上一次全量备份或增量备份开始，备份所有修改后的宏块，即仅针对已备份数据的增量部分进行备份。例如，假设当前租户最近一次是全量备份数据 `full_backup_set`，系统就会备份最近一次`full_backup_set` 之后修改的所有宏块；如果租户最近一次是增量备份数据 `inc_backup_set`，则系统就会备份最近一次 `inc_backup_set` 之后修改的所有宏块；

#### 2.1 使用限制及注意事项

- 执行增量数据备份需要确保已经有全量数据备份存在，如果在不存在全量数据备份的情况下直接发起增量备份，系统默认会将增量备份转换为全量备份。
    
- 对于低版本集群升级到高版本集群的场景，即使当前租户在低版本中已经执行了全量备份，升级到高版本的集群中后，如果直接发起增量备份，系统默认也会将增量备份转换为全量备份。
    
#### 2.2 发起增量备份
完成准备工作后，您可以通过以下方式发起增量数据备份。

假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`。


##### 2.2.1 系统租户发起增量备份
`sys` 租户可以对集群中的所有租户或指定租户发起增量数据备份；

备份并发度由租户级配置项 `ha_low_thread_score` 控制，该配置项用于指定备份、备份清理等中低优先级别任务队列使用线程时间片的权重值，取值范围为 [0, 100]，默认值为 `0`。在开始备份前，可以适当提高配置项 `ha_low_thread_score` 的值，建议每次将数值翻倍。查看配置项 `ha_low_thread_score` 的相关操作，请参见 [查看数据备份相关参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218411)，；

有关配置项 `ha_low_thread_score` 的详细说明，请参见 [ha_low_thread_score](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220343)，；
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2.（可选）设置租户的备份并发度
	-- 设置集群中所有租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all_user;
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = all;

	-- 设置集群中指定租户的备份并发度
	obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10 TENANT = mysql_tenant;
```

> [!NOTE] `说明`：
> OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；

```sql
-- 3. 执行以下语句，发起增量数据备份
	-- 对集群中的所有租户发起增量数据备份
		-- 该方式会对集群中的所有租户发起增量数据备份;
		obclient [(none)]> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;
			-- 命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 和 `oracle_tenant` 租户发起增量数据备份；

	-- 对集群中指定租户发起增量数据备份
		-- 该方式仅对指定租户发起增量数据备份，不会影响集群中的其他租户；
		-- 对 `mysql_tenant` 租户发起增量数据备份的示例如下：
		obclient [(none)]> ALTER SYSTEM BACKUP INCREMENTAL TENANT = mysql_tenant;
			-- 同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；
```
命令执行成功后，在本示例中，系统会对集群中的 `mysql_tenant` 租户发起增量数据备份，；
增量备份过程中，可以实时查看增量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


##### 2.2.2 用户租户发起增量备份
用户租户可以对本租户发起增量数据备份，不会影响其他租户；

备份并发度由租户级配置项 `ha_low_thread_score` 控制，该配置项用于指定备份、备份清理等中低优先级别任务队列使用线程时间片的权重值，取值范围为 [0, 100]，默认值为 `0`。在开始备份前，可以适当提高配置项 `ha_low_thread_score` 的值，建议每次将数值翻倍。查看配置项 `ha_low_thread_score` 的相关操作，请参见 [查看数据备份相关参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218411)，；

有关配置项 `ha_low_thread_score` 的详细说明，请参见 [ha_low_thread_score](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220343)，；

```sql
-- 1. 租户管理员登录到数据库；
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.（可选）设置备份并发度；
obclient [(none)]>  ALTER SYSTEM SET ha_low_thread_score = 10;
```

```sql
-- 3. 执行以下语句，发起增量数据备份
obclient [(none)]> ALTER SYSTEM BACKUP INCREMENTAL DATABASE;
```
命令执行成功后，系统会对本租户发起增量数据备份，；
增量备份过程中，可以实时查看增量备份进度，具体操作请参见 [查看数据备份进度](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218414)，；


### 3 停止备份
发起数据备份后，您可以停止正在运行的备份任务。停止备份后，如果开启了自动清理备份的功能，则备份清理任务会及时清理不完整的备份；

#### 3.1 前提条件

由于停止正在运行的备份任务后，系统会产生一些不完整的备份数据。建议您提前开启自动清理备份功能，及时清理不完整的备份，以免占用资源。开启自动清理备份功能的相关操作，请参见 [自动清理过期备份](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218393)。

#### 3.2 背景信息

假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`，且租户 `mysql_tenant` 和 `oracle_tenant` 都正在进行数据备份。

#### 3.3 租户停止备份
````tab
tab: 系统租户停止备份
`sys` 租户可以停止集群中所有租户或指定租户的数据备份；
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2.执行以下语句，停止数据备份；
	-- 停止集群中所有租户的数据备份
	obclient [(none)]> ALTER SYSTEM CANCEL BACKUP;
		-- 该方式会停止集群中所有租户的数据备份任务；

	-- 停止集群中指定租户的数据备份
	obclient [(none)]> ALTER SYSTEM CANCEL BACKUP TENANT = mysql_tenant;
		-- 该方式仅停止指定租户正在执行的数据备份任务，不会影响集群中的其他租户；
		-- 同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；
```

tab: 用户租户停止备份
用户租户可以停止本租户正在执行的数据备份任务，不会影响其他租户；
```sql
-- 1.租户管理员登录到数据库；
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.执行以下语句，停止数据备份
	obclient [(none)]> ALTER SYSTEM CANCEL BACKUP;
```
````


## 1 参考文档
1. *发起全量数据备份*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218406)，；
2. *发起增量数据备份*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218410)，；
3. *停止备份*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218413)，；



