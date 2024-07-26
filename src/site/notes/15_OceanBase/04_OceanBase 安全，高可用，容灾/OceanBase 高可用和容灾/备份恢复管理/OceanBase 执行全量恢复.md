---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行全量恢复/","dgPassFrontmatter":true}
---


### OceanBase 执行全量恢复
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


#### 2 执行全量恢复
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户；

-- 2.（可选）执行以下语句，设置备份的恢复密码；
	该步骤在【恢复前准备】中，已执行；
```

```sql
-- 3.执行以下语句，开始执行恢复任务；
	-- 语法：
	ALTER SYSTEM RESTORE <dest_tenant_name> FROM <source_tenant_name> at 'uri' UNTIL 'timestamp' WITH 'restore_option';
/*
dest_tenant_name：指恢复的新租户的名称；
source_tenant_name：指源集群的租户；
uri：指备份时设置的 `backup_dest`；
timestamp：指恢复的时间戳，需要大于等于最早备份的数据备份的 CDB_OB_BACKUP_SET_DETAILS 的 START_TIME，
	小于等于日志备份 CDB_OB_BACKUP_ARCHIVELOG_SUMMARY 的 MAX_NEXT_TIME；
restore_option：支持 backup_cluster_name、backup_cluster_id、pool_list、locality、primary_zone、kms_encrypt：
	backup_cluster_name 为必选项，填写源集群的名称；
	backup_cluster_id 为必选项，填写源集群的 cluster_id；
	pool_list 为必选项，填写用户的资源池。多个资源池之间用英文逗号（,）分隔；
*/

-- 恢复示例：
	-- NFS：
	ALTER SYSTEM RESTORE restored_trade FROM trade at 'file:///data/nfs/backup' until '2020-05-21 09:39:54.071670' with 'backup_cluster_name=xxx&backup_cluster_id=xxx&pool_list=xxx';
	-- OSS：
	ALTER SYSTEM RESTORE restored_trade FROM trade at 'oss://oceanbase-test-bucket/backup/?host=xxx.aliyun-inc.com&access_id=xxx&access_key=xxx' until '2020-03-23 08:59:45' with 'backup_cluster_name=ob20daily.backup&backup_cluster_id=1&pool_list=restore_pool';
	-- OBS：
	ALTER SYSTEM RESTORE restored_trade from trade at 'obs://oceanbase-test-appid/backup?host=obs.cn-east-2.myhuaweicloud.com&access_id=xxx&access_key=xxx&appid=xxx' until ' 2020-03-23 08:59:45' with 'backup_cluster_name=ob20daily.backup&backup_cluster_id=1&pool_list=restore_pool';
```
还可以在 OCP 上对已备份的集群发起恢复操作，详细情况查看：【参考文档】；


#### 3 取消恢复
在恢复租户数据的过程中，您可以通过删除待恢复数据的租户的方式来取消恢复；
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户；

-- 2.执行以下命令，立即删除待恢复数据的租户；
	obclient> DROP TENANT tenant_name FORCE;
		-- tenant_name：需要填写待恢复的租户名称；
```
关于删除租户的更多操作及说明信息，请参见 [删除租户](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946252)，；


### 参考文档
1. *恢复前准备*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218384)，；
2. *执行全量恢复*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946221)，；
3. *恢复指定的表*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946222)，；


