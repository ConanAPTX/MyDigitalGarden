---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行备份备份数据的恢复/","dgPassFrontmatter":true}
---


### OceanBase 执行备份备份数据的恢复
#### 1 恢复前准备

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base/ocean-base//ocean-base/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 恢复前准备
在执行恢复操作前，您需要创建待恢复的租户所需的资源；

##### 1.1 注意事项
在进行恢复操作前，请务必确认待恢复的备份数据的版本，OceanBase 数据库当前仅支持将低版本的备份数据恢复到同版本或高版本中，同版本下的小版本之间也不支持逆向恢复；

例如：如果您的备份数据为 OceanBase 数据库 V3.2.4 版本，则仅支持将该数据恢复到 OceanBase 数据库 V3.2.4 及以上版本中；如果您的备份数据为 OceanBase 数据库 V4.2.0 版本，则仅支持将该数据恢复到 V4.2.x 版本中；同时，对于 OceanBase 数据库 V4.2.0 版本，支持恢复 V4.1.x 版本的数据；

同时，对于 OceanBase 数据库 V3.2.4 版本，当前支持恢复 OceanBase 数据库 V2.2.7x 和 V3.1.2 bp2（20210813154332）及其以上的版本的备份数据；

##### 1.2 恢复前准备
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户；

-- 2.创建 Unit（资源单元）
CREATE RESOURCE UNIT box_16c96g max_cpu 16, max_memory 103079215104, max_iops 10240, max_disk_size 53687091200, 
	max_session_num 64, MIN_CPU=16, MIN_MEMORY=103079215104, MIN_IOPS=10240;

-- 3.创建 Resource Pool（资源池）
	CREATE RESOURCE POOL restore_pool unit = 'box_16c96g', unit_num = 1, zone_list = ('z1','z2','z3');
```

```sql
-- 4.执行以下语句，设置加密信息
	-- 说明：如果未加密或恢复时可以访问原来的密钥管理服务，则跳过本步骤；
	obclient> SET @kms_encrypt_info = '<加密string>';
		-- <加密string>：为 `EXTERNAL_KMS_INFO` 的值，`EXTERNAL_KMS_INFO` 为租户级配置项；
```
说明：`external_kms_info` 用于存储一些密钥管理信息。有关该配置项的详细介绍，请参见 [external_kms_info](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220339)，；

```sql
-- 5.打开恢复配置
	-- 检查 restore_concurrency 是否为 0，0 表示关闭，您可按需设定。如果为 0 ，则需要执行以下语句：
	obclient> ALTER SYSTEM SET restore_concurrency = 10;

-- 6.（可选）修改恢复的等待时间
	-- 默认恢复等待时间 `_restore_idle_time` 为 1 分钟，整个恢复期间会有 3 次等待，即 3 分钟的等待时间；
	obclient> ALTER SYSTEM SET _restore_idle_time = '10s';
		-- 对于测试恢复性能的场景，为了减少恢复的空闲时间，您可以执行以下语句将等待时间调整为 `10s`；
```

`7.（可选）设置恢复的密码`：
仅在数据备份时添加了密码的场景下才需要设置备份的恢复密码；
```sql
-- 语法：SET DECRYPTION IDENTIFIED BY 'password';
-- 参数：password：需要替换为备份时添加的密码；

SET DECRYPTION IDENTIFIED BY 'password';                -- password 需要替换为备份时添加的密码；
SET DECRYPTION IDENTIFIED BY 'password1'，'password2';  -- 同时如果全量备份 + 增量备份设置的密码不一样，则需要输入多个密码；
```

同时如果全量备份 + 增量备份设置的密码不一样，则需要输入多个密码，密码之间使用逗号分隔，全量备份密码放在前面，增量备份密码放在后面；



</div></div>


#### 2 执行备份备份数据的恢复
基于备份备份的恢复；
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户；

-- 2.执行以下命令，开始执行恢复任务。
	ALTER SYSTEM RESTORE dest_tenant_name FROM source_tenant_name AT 'uri' UNTIL 'timestamp' WITH 'restore_option' PREVIEW;
	/*
	参数说明如下表所示：
	    dest_tenant_name：指恢复的新租户的名称；
	    source_tenant_name：指源集群的租户；
	    uri：指备份时设置的 `backup_dest`；
	    timestamp：指恢复的时间戳，需要大于等于最早备份的数据备份的 CDB_OB_BACKUP_SET_DETAILS 的 START_TIME，
		    小于等于日志备份 CDB_OB_BACKUP_ARCHIVELOG_SUMMARY 的MAX_NEXT_TIME；
		restore_option：；
	*/   
	    
	-- 恢复 PREVIEW 示例：
	ALTER SYSTEM RESTORE restored_trade FROM trade AT 'file:///data/nfs/backup' until '2020-05-21 09:39:54.071670' with 'backup_cluster_name=ob20daily.backup&backup_cluster_id=1&pool_list=restore_pool' PREVIEW;

```

```sql
-- 3.执行 SHOW RESTORE PREVIEW 来获取恢复数据的路径
obclient> SHOW RESTORE PREVIEW;
+--------------+-----------+---------+------------------------------------------------------------------------------------------------------------+-------------+
| backup_type  | backup_id | copy_id | backup_dest                                                                                                | file_status |
+--------------+-----------+---------+------------------------------------------------------------------------------------------------------------+-------------+
| BACKUP_SET   |         1 |       0 | file:///data/nfs/backup/1/incarnation_1/1002/data/backup_set_1_full_20211225                               | AVAILABLE   |
| BACKUP_SET   |         2 |       0 | file:///data/nfs/backup/1/incarnation_1/1002/data/backup_set_2_inc_20211225                                | AVAILABLE   |
| BACKUP_SET   |         1 |       1 | file:///data/nfs/backup_backup/1/incarnation_1/1002/data/backup_set_1_full_20211225                        | AVAILABLE   |
| BACKUP_SET   |         2 |       1 | file:///data/nfs/backup_backup/1/incarnation_1/1002/data/backup_set_2_inc_20211225                         | AVAILABLE   |
| BACKUP_PIECE |         1 |       0 | file:///data/nfs/backup/1/incarnation_1/1002/clog/1_1_20211225                                             | AVAILABLE   | 
| BACKUP_PIECE |         2 |       0 | file:///data/nfs/backup/1/incarnation_1/1002/clog/1_2_20211225                                             | AVAILABLE   |
| BACKUP_PIECE |         2 |       1 | file:///data/nfs/backup_backup/1/incarnation_1/1002/clog/1_2_20211225                                      | AVAILABLE   |
+--------------+-----------+---------+------------------------------------------------------------------------------------------------------------+-------------+
```
 
4.  通过 `ADD RESTORE SOURCE` 语句来加载需要恢复的路径；

> **说明**：
> `xxx` 表示上文展示的路径。需要分别加载 `BACKUP_SET` 和 `BACKUP_PIECE` 对应的恢复路径，并且对于每一个 `backup_id` 相同的 `BACKUP_SET` 或 `BACKUP_PIECE`，只需要选择其中一个 `copy_id` 对应的路径加入即可；


```sql
-- 4.通过 ADD RESTORE SOURCE 语句来加载需要恢复的路径
obclient> ALTER SYSTEM ADD RESTORE SOURCE 'xxx';

-- 例如，根据上一步的查询结果，对于 BACKUP_SET 和 BACKUP_PIECE：
	-- BACKUP_SET：
		-- `backup_id` 为 `1` 的 `BACKUP_SET`，可以选择 `copy_id` 为 `0` 或 `1` 对应的路径，语句如下：
			ALTER SYSTEM ADD RESTORE SOURCE 'file:///data/nfs/backup/1/incarnation_1/1002/data/backup_set_1_full_20211225';
			ALTER SYSTEM ADD RESTORE SOURCE 'file:///data/nfs/backup_backup/1/incarnation_1/1002/data/backup_set_1_full_20211225';
		-- `backup_id` 为 `2` 的 `BACKUP_SET`，选择 `copy_id` 为 `0` 对应的路径，语句如下：
			ALTER SYSTEM ADD RESTORE SOURCE 'file:///data/nfs/backup/1/incarnation_1/1002/data/backup_set_2_inc_20211225';
	
	-- BACKUP_PIECE：
		-- `backup_id` 为 `1` 的 `BACKUP_PIECE`，选择 `copy_id` 为 `0` 对应的路径，语句如下：
			ALTER SYSTEM ADD RESTORE SOURCE 'file:///data/nfs/backup/1/incarnation_1/1002/clog/1_1_20211225';
		-- `backup_id` 为 `2` 的 `BACKUP_PIECE`，可以选择 `copy_id` 为 `0` 或 `1` 对应的路径，语句如下：
			ALTER SYSTEM ADD RESTORE SOURCE 'file:///data/nfs/backup/1/incarnation_1/1002/clog/1_2_20211225';
			ALTER SYSTEM ADD RESTORE SOURCE 'file:///data/nfs/backup_backup/1/incarnation_1/1002/clog/1_2_20211225';

-- 如果输入错误，您可执行下述语句来撤销之前的输入：
ALTER SYSTEM CLEAR RESTORE SOURCE;
```
  
5.  调用恢复命令；
```sql
ALTER SYSTEM RESTORE <$restore_tenant> FROM <$backup_tenant> UNTIL '<$restore_checkpoint>' 
	WITH 'backup_cluster_name=$backup_cluster_name&backup_cluster_id=$backup_cluster_id&pool_list=$resource_pool_list&locality=F@$zone_list&primary_zone=$primary_zone_list';
/*
与普通的物理恢复不同，基于备份备份的多目的端恢复要求 SQL 语句中不带 `at 'backup_dest'` 字段。其中，
	$restore_tenant：表示恢复的租户名；
	$backup_tenant：表示备份时的租户名；
	$restore_checkpoint：表示希望恢复到的时间戳；
*/

-- 使用示例：
ALTER SYSTEM RESTORE test_restore_tenant FROM test_backup_tenant UNTIL '2020-05-21 09:39:54.071670' 
	WITH 'backup_cluster_name=test_oceanbase&backup_cluster_id=1&pool_list=small_pool_2&locality=F@z1&primary_zone=z1';
```


### 参考文档



