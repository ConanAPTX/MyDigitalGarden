---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 手动触发，控制合并_V4.0.0 及以后版本/","dgPassFrontmatter":true}
---


### OceanBase 手动触发，控制合并_V4.0.0 及以后版本
#### 1 手动触发合并
合并也可以通过手动触发来完成。手动合并包括租户级合并和分区级合并；

*自适应合并* 实际调度的就是*分区级合并*，只是由系统适时地对有需求的分区发起分区合并任务。同时，分区级合并的使用场景与自适应合并解决的场景相同，即主要针对导数、频繁 DML、查询效率低等场景，有关自适应合并的详细介绍，请参见 [自适应合并](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819294)，；

如果您因业务需要关闭了自适应合并功能，遇到查询慢等问题，您可以使用手动方式发起分区级合并来解决这些问题；

##### 1.1 注意事项
> [!NOTE] 分区与 Tablet 一一对应，在进行分区级合并时，需要注意以下事项：
> -   当对应的分区正在执行租户级合并任务时，无法发起分区级合并；
> -   当对应的分区正在执行自适应调度的合并任务时，无法发起分区级合并；
> -   当对应分区的多副本处于不一致状态时，无法发起分区级合并；
> -   当对应分区处于恢复中或 Transfer 状态时，无法发起分区级合并；
> -   当合并任务被暂停时，不允许发起分区级合并；
> -   分区级合并的实质为对同一分区的多个副本执行 Major Compaction 任务，会消耗 CPU 及磁盘 I/O。在执行分区级合并操作前，您需要权衡当前租户的资源占用情况，并且当分区级合并命令执行成功后，会出现 CPU、I/O 占用升高的情况；


##### 1.2 前提条件

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02///#2-1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 2.1 前提条件

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02///#1-1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 1.1 前提条件
触发合并前，需要确保全局合并开关已开启，全局合并开关由集群级配置项 *enable_major_freeze* 控制，默认值为 *True* ，表示开启合并开关；
```sql
--1.使用 root 用户登录到数据库的 sys 租户

--2.通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'enable_major_freeze';

--3.是否开启自动全局冻结的功能
ALTER SYSTEM SET enable_major_freeze='True';    -- 开启
ALTER SYSTEM SET enable_major_freeze='False';   -- 关闭
```
关于配置项 `enable_major_freeze` 的更多信息，请参见 [enable_major_freeze](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821020)，；


</div></div>




</div></div>


##### 1.3 系统租户手动发起合并

V4.0.0 之前版本仅支持【`集群级`】合并；从 V4.0.0 及之后版本开始支持【`租户级`】合并；
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 2. 执行以下命令，发起合并；
-- 2.1 发起租户级别的合并
	-- 语法：LTER SYSTEM MAJOR FREEZE TENANT [=] all_user | all | all_meta | tenant_name [, tenant_name ...];
		-- 其中，`[=]` 表示等号（=）为可选；
	-- 系统租户对本租户发起合并
	obclient> ALTER SYSTEM MAJOR FREEZE TENANT;

	-- 系统租户对所有用户租户发起合并
	obclient> ALTER SYSTEM MAJOR FREEZE TENANT = all;
	obclient> ALTER SYSTEM MAJOR FREEZE TENANT = all_user;   -- 从 V4.2.1 开始支持，后续版本推荐使用；
		--  OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，
			-- 推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；
			
	-- 系统租户对所有 Meta 租户发起合并
		obclient> ALTER SYSTEM MAJOR FREEZE TENANT = all_meta;

	-- 系统租户对指定租户发起合并
		obclient> ALTER SYSTEM MAJOR FREEZE TENANT = tenant1,tenant2;
```

OceanBase 从 V3.1.0 版本开始支持【`分区级`】合并；
```sql
-- 2. 执行以下命令，发起合并；
-- 2.2 发起分区级的合并
	-- V4.2.0 版本不支持，V4.3.1 版本支持；
	-- 语法：ALTER SYSTEM MAJOR FREEZE TENANT [=] tenant_name TABLET_ID = tablet_id;
	obclient> ALTER SYSTEM MAJOR FREEZE TENANT = tenant1 TABLET_ID = 200001;  -- 系统租户对租户 tenant1 发起分区级合并的示例如下

	-- 查询表信息
		-- 从 V4.0.0 版本开始引入；视图 `CDB_OB_TABLE_LOCATIONS` 用于展示表或者分区所在的位置，包括：系统表、用户表、索引表等；
	obclient [oceanbase]> SELECT * FROM oceanbase.CDB_OB_TABLE_LOCATIONS WHERE TENANT_ID = 1004;
	obclient [oceanbase]> SELECT * from oceanbase.CDB_OB_TABLE_LOCATIONS  
	WHERE TENANT_ID = '' and DATABASE_NAME = '' and TABLE_NAME = ''
```
其中，`tablet_id` 可以由系统租户通过查询视图 `CDB_OB_TABLE_LOCATIONS` 来获取。有关视图 `CDB_OB_TABLE_LOCATIONS` 中各字段的详细介绍，请参见 [oceanbase.CDB_OB_TABLE_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820159)，；

`注意`：分区级合并命令与租户级合并以及自适应调度策略发起的合并均存在互斥关系，命令执行成功并不代表分区合并发起成功。您可以根据视图 `GV$OB_MERGE_INFO` 查看指定分区中 `ACTION='MEDIUM_MERGE'` 的合并信息，或者根据视图 `GV$OB_TABLET_COMPACTION_HISTORY` 查看指定分区中 `TYPE='MEDIUM_MERGE'` 的合并信息来确认合并是否成功发起。有关查看合并过程的详细操作，请参见：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819295)，；


##### 1.4 用户租户手动发起合并
从 V4.0.0 版本开始支持【`租户级`】合并；用户租户只能对本租户发起租户级别和分区级别的合并操作；
```sql
-- 1. 用户租户的租户管理员登录到数据库；

-- 2. 对本租户发起租户级合并；
	-- 发起本租户的合并
		obclient> ALTER SYSTEM MAJOR FREEZE;   -- 对本租户发起合并
	
	-- 发起分区级别的合并
		-- V4.2.0 版本不支持，V4.3.1 版本支持；
		-- 语法：ALTER SYSTEM MAJOR FREEZE TABLET_ID = tablet_id;
		obclient> ALTER SYSTEM MAJOR FREEZE TABLET_ID = 200001;

	-- 查询表信息
		-- 展示表或者分区所在的位置，包括：系统表、用户表、索引表等；从 V4.0.0 版本开始引入；
		obclient [oceanbase]> SELECT * FROM oceanbase.CDB_OB_TABLE_LOCATIONS 
		WHERE DATABASE_NAME = '' AND TABLE_NAME = '';	
```
其中，`tablet_id` 可以通过查询视图 `DBA_OB_TABLE_LOCATIONS` 来获取。有关视图 `DBA_OB_TABLE_LOCATIONS` 中各字段的详细介绍，参见 [DBA_OB_TABLE_LOCATIONS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820604)，；

`注意`：分区级合并命令与租户级合并以及自适应调度策略发起的合并均存在互斥关系，命令执行成功并不代表分区合并发起成功。用户租户可以根据视图 `GV$OB_TABLET_COMPACTION_HISTORY` 查看指定分区中 `TYPE='MEDIUM_MERGE'` 的合并信息来确认合并是否成功发起。有关查看合并过程的详细操作，请参见 [查看合并过程](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819295)，；

#### 2 手动控制合并
通过 `MAJOR FREEZE` 命令触发合并后，系统会自动调度合并任务。不同于 OceanBase 数据库 V2.x.x 和 V3.x.x 版本，从 OceanBase 数据库 V4.0.x 版本开始，合并将采用统一合并策略来执行合并流程，即租户的所有 Zone 同时开始执行合并，且所有 Zone 均完成合并后，才表示该租户完成合并；

合并过程中，不再需要您手动控制各个 Zone 的合并顺序等事项，也不存在轮转合并。如果在合并过程中出现错误，您可以参考本文手动控制合并。例如，Checksum 校验出错，会使得该租户当前轮次的合并无法结束，您可以暂停当前合并(suspend merge), 待 Checksum 恢复正确以后（例如，人工介入修复），再清理 Checksum 错误的标记（clear merge error）, 然后继续执行当前轮次的合并 (resume merge)；

##### 2.1 前提条件
手动控制合并前，请确认已通过 `MAJOR FREEZE` 命令触发合并，手动触发合并的详细操作，请参见：*手动触发合并*：[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886130)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819291)，；

##### 2.2 注意事项
当合并任务被暂停时，允许发起租户级合并，但不允许发起分区级合并。对于合并任务暂停时发起的租户级合并，系统并不会立即执行本次租户级合并任务，而是在恢复合并后才执行本次租户级合并；


##### 2.3 系统租户手动控制合并
合并开始后，系统租户可以控制所有租户或指定租户的合并操作。可以控制的合并操作包括租户级合并和分区级合并；

具体操作如下：
1. 使用 `root` 用户登录集群的 `sys` 租户；
```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

```

2. 根据业务实际情况，选择合适的命令控制合并；

`2.1 暂停所有用户租户、所有 Meta 租户或指定租户的合并`；
开始合并后，如果需要暂停所有用户租户、所有 Meta 租户或指定租户的合并，可以执行以下语句；

```sql
-- 2. 根据业务实际情况，选择合适的命令控制合并；
-- 2.1 暂停所有用户租户、所有 Meta 租户或指定租户的合并；
	-- 暂停所有用户租户的合并
	ALTER SYSTEM SUSPEND MERGE TENANT = all;
	ALTER SYSTEM SUSPEND MERGE TENANT = all_user;    -- 从 V4.2.1 开始支持，后续版本推荐使用；
		-- OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，
			-- 推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；
		
	-- 暂停所有 Meta 租户的合并；
		-- V4.0.0 版本不支持，V4.3.1 版本支持；
	ALTER SYSTEM SUSPEND MERGE TENANT = all_meta;

	-- 暂停指定租户的合并；
	ALTER SYSTEM SUSPEND MERGE TENANT = tenant1，tenant2;
		-- 其中，`=` 为可选; `tenant1`、`tenant2` 需要替换为实际的租户名；
```

`2.2 恢复合并`
暂停合并后，也可以恢复合并，语句如下；
```sql
-- 2. 根据业务实际情况，选择合适的命令控制合并；
-- 2.2 恢复合并
	-- 恢复所有用户租户的合并；
	ALTER SYSTEM RESUME MERGE TENANT = all;
	ALTER SYSTEM RESUME MERGE TENANT = all_user;    -- 从 V4.2.1 开始支持，后续版本推荐使用；
		--  OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，
			-- 推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；

	-- 恢复所有 Meta 租户的合并；
		-- V4.0.0 版本不支持，V4.3.1 版本支持；
	ALTER SYSTEM RESUME MERGE TENANT = all_meta;

	-- 恢复指定租户的合并
	ALTER SYSTEM RESUME MERGE TENANT = tenant1，tenant2;
		-- 其中，`=` 为可选；`tenant1`、`tenant2` 需要替换为实际的租户名；
```

`2.3 清理 Checksum 错误的标记；`
如果在合并过程中出现了 Checksum 校验错误，且人工介入修复后，可以清理掉该 Checksum 错误的标记，以恢复合并，语句如下；
```sql
-- 2. 根据业务实际情况，选择合适的命令控制合并；
-- 2.3 清理 Checksum 错误的标记；
	-- 清理所有用户租户的 Checksum 错误的标记；
	ALTER SYSTEM CLEAR MERGE ERROR TENANT = all;
	ALTER SYSTEM CLEAR MERGE ERROR TENANT = all_user;   -- 从 V4.2.1 开始支持，后续版本推荐使用；
		-- OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，
			-- 推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；

	-- 清理所有 Meta 租户的 Checksum 错误的标记；
		-- V4.0.0 版本不支持，V4.3.1 版本支持；
	ALTER SYSTEM CLEAR MERGE ERROR TENANT = all_meta;

	-- 清理指定租户的 Checksum 错误的标记；
	ALTER SYSTEM CLEAR MERGE ERROR TENANT = tenant1，tenant2;
		-- 其中，`=` 为可选；`tenant1`、`tenant2` 需要替换为实际的租户名；
```
OceanBase 数据库从 V4.2.1 版本开始，`TENANT = all_user` 与 `TENANT = all`语义相同，在需要生效范围为所有用户租户时，推荐使用 `TENANT = all_user`，后续 `TENANT = all` 将废弃不再使用；


##### 2.4 用户租户手动控制合并
合并开始后，用户租户只能控制本租户的合并操作。可以控制的合并操作包括租户级合并和分区级合并；

```sql
-- 1. 用户租户的租户管理员登录到数据库；

-- 2. 根据业务实际情况，选择合适的命令控制合并；
	-- 2.1 暂停本租户的合并；
	obclient> ALTER SYSTEM SUSPEND MERGE;  -- 开始合并后，如果需要暂停本租户的合并，可以执行以下语句；

	-- 2.2 恢复合并
	obclient> ALTER SYSTEM RESUME MERGE;   -- 暂停合并后，也可以恢复合并；

	-- 2.3 清理 Checksum 错误的标记
		-- 如果在合并过程中出现了 Checksum 校验错误，且人工介入修复后，可以清理掉该 Checksum 错误的标记，以恢复合并；
	obclient> ALTER SYSTEM CLEAR MERGE ERROR;
```



### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02//ocean-base/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 参考文档：
1. [存储架构概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354478)，介绍 OB 存储架构，及存储引擎的功能：数据存储，转储合并，查询读写，数据校验；
	 1. [转储和合并](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355112)，转储和合并概述；
2. [管理数据存储](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括转储管理，合并管理，数据压缩；
	1. [转储管理](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括自动触发转储，自动触发转储，手动触发转储，查询转储信息，修改转储配置；
	2. [OceanBase 数据库的分层转储功能](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000023?back=kb)，介绍 OceanBase 数据库的分层转储功能；
	3. [LSM-Tree 和 OceanBase 分层转储](https://my.oschina.net/actiontechoss/blog/8563823)，详细分层转储原理及相关参数介绍；
3. [如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；
4. *合并管理概述*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819293)，；
5. *自动触发合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819289)，；
6. *定时触发合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819290)，；
7. *自适应合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819294)，；
8. *手动触发合并*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946263)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886130)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819291)，；
9. *手动控制合并*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001417800)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001467935)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819292)，；
10. *查看合并信息*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945932)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886131)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819295)，；
	1. [如何查看 Compaction 的当前合并进度及历史记录](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209910?back=kb)，；
	2. [查询合并过程_v4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886131)，；
	3. [转储查看](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376777)，；


</div></div>



