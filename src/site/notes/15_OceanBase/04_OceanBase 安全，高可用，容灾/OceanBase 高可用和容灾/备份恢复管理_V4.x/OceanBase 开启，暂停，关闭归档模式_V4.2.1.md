---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 开启，暂停，关闭归档模式_V4.2.1/","dgPassFrontmatter":true}
---


### OceanBase 开启，暂停，关闭归档模式_V4.2.1

#### 1 开启归档模式
归档模式用于控制某个租户的日志归档功能，只有在归档模式下，才可以进行日志归档任务。若租户在开启归档模式之前，已经配置了归档目的端，并且该目的端处于 `ENABLE` 状态，开启归档模式后，系统将会自动发起日志归档任务；
##### 1.1 背景信息
假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`，且租户 `mysql_tenant` 和 `oracle_tenant` 均未开启归档模式，您可以通过以下 3 种方式为租户开启归档模式；

##### 1.2 为租户开启归档模式

````tab
tab: 系统租户为其他租户开启归档模式
`sys` 租户可以为集群中的所有租户或指定租户开启归档模式；
```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；

-- 2.执行以下语句，开启归档模式
	-- 开启集群中所有租户的归档模式；
		-- 该方式会将当前集群中的所有租户开启归档模式；
		obclient [(none)]> ALTER SYSTEM ARCHIVELOG [TENANT = ALL];
			-- 注意：该操作仅对当前集群中的所有租户生效，对于该操作之后所创建的租户，系统不会自动开启归档模式；

	-- 开启指定租户的归档模式
		ALTER SYSTEM ARCHIVELOG TENANT = mysql_tenant;   -- 开启 mysql_tenant 租户的日志归档；
			-- 说明：同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；
```

tab: 用户租户为本租户开启归档模式
用户租户也可以直接为本租户开启归档模式，不会影响其他租户；

```sql
-- 1.租户管理员登录到数据库；
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.执行以下语句，开启本租户的归档模式
obclient [xxx]> ALTER SYSTEM ARCHIVELOG;
```
````


##### 1.3 查看租户的归档模式

````tab
tab: sys 租户
```sql
-- 1.查看集群中所有租户的归档模式
	SELECT TENANT_NAME, LOG_MODE FROM oceanbase.DBA_OB_TENANTS WHERE TENANT_TYPE = 'USER'\G
	/*
	TENANT_NAME：租户名称；
	LOG_MODE：归档模式状态；NOARCHIVELOG：关闭；ARCHIVELOG：开启；
	*/
```


tab: 业务租户
```sql
-- 2.查看该租户所处的归档模式
	obclient [SYS]> SELECT TENANT_NAME, LOG_MODE FROM DBA_OB_TENANTS\G
	/*
	TENANT_NAME：租户名称；
	LOG_MODE：归档模式状态；NOARCHIVELOG：关闭；ARCHIVELOG：开启；
	*/
```
````


#### 2 暂停归档
开启归档模式后，您可以通过将目的端的归档状态更改为 `DEFER` 的方式来暂停归档；
```sql
-- 目的端的归档状态及说明如下：
	-- ENABLE：目的端的归档处于启用状态。默认情况下，目的端的归档状态处于 `ENABLE` 状态；
	-- DEFER：目的端的归档处于暂停状态。处于该状态的目的端，如果有归档任务，系统将会暂停归档任务；

-- 1.`sys` 租户或用户租户的租户管理员登录到数据库；
	-- 说明：MySQL 模式的管理员用户为 `root` 用户，Oracle 模式的管理员用户为 `SYS` 用户；

-- 2.暂停目的端的归档；
	-- 系统租户暂停指定租户目的端的归档
	ALTER SYSTEM SET LOG_ARCHIVE_DEST_STATE='DEFER' TENANT = tenant_name;

	-- 用户租户暂停本租户目的端的归档
	ALTER SYSTEM SET LOG_ARCHIVE_DEST_STATE='DEFER';
```

#### 3 关闭归档模式
开启归档模式后，也可以用相同的方式关闭归档模式。若租户在关闭归档模式之前，有正在进行或者暂停的日志归档任务，关闭归档模式后，系统会自动停止这些归档任务；

> [!NOTE] `背景信息`：
> 假设当前集群中有 3 个租户，分别是 `sys`、`mysql_tenant` 和 `oracle_tenant`，且租户 `mysql_tenant` 和 `oracle_tenant` 均已开启归档模式；

````tab
tab: 系统租户为其他租户关闭归档模式
`sys` 租户可以关闭集群中所有租户或指定租户的归档模式；

```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户

-- 2.执行以下语句，关闭归档模式
	-- 关闭集群中所有租户的归档模式
		-- 该方式会关闭集群中所有租户的归档模式；
		obclient [(none)]> ALTER SYSTEM NOARCHIVELOG [TENANT = ALL];

	-- 关闭集群中指定租户的归档模式
		-- 该方式仅会关闭指定租户的归档模式，不会影响集群中的其他租户；
		obclient [(none)]> ALTER SYSTEM NOARCHIVELOG TENANT = mysql_tenant;
			-- 说明：同时指定多个租户时，租户名之间使用英文逗号 (`,`) 分隔；
```

tab: 用户租户为本租户关闭归档模式
用户租户也可以直接关闭本租户的归档模式，不会影响其他租户；

```sql
-- 1.租户管理员登录到数据库；
	-- 本示例中，您可以使用 `root` 用户登录 `mysql_tenant` 租户；或者也可以使用 `SYS` 用户登录 `oracle_tenant` 租户；

-- 2.执行以下语句，关闭归档模式
obclient [xxx]> ALTER SYSTEM NOARCHIVELOG;
```
````


##### 3.1 查看租户的归档模式

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base/ocean-base/v4-x/ocean-base-v4-2-1/#1-3" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 1.3 查看租户的归档模式

````tab
tab: sys 租户
```sql
-- 1.查看集群中所有租户的归档模式
	SELECT TENANT_NAME, LOG_MODE FROM oceanbase.DBA_OB_TENANTS WHERE TENANT_TYPE = 'USER'\G
	/*
	TENANT_NAME：租户名称；
	LOG_MODE：归档模式状态；NOARCHIVELOG：关闭；ARCHIVELOG：开启；
	*/
```


tab: 业务租户
```sql
-- 2.查看该租户所处的归档模式
	obclient [SYS]> SELECT TENANT_NAME, LOG_MODE FROM DBA_OB_TENANTS\G
	/*
	TENANT_NAME：租户名称；
	LOG_MODE：归档模式状态；NOARCHIVELOG：关闭；ARCHIVELOG：开启；
	*/
```
````



</div></div>



### 参考文档
1. *日志归档的准备工作*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218397)，；
2. *开启归档模式*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218401)，；
3. *暂停归档*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000608973)，；
4. *关闭归档模式*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218404)，；


