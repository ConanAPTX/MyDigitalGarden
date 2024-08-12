---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复/OceanBase 执行物理恢复/","dgPassFrontmatter":true}
---


### OceanBase 执行物理恢复
本节主要介绍如何通过命令的方式进行租户级物理恢复，目前 OceanBase 数据库支持租户级别的基于时间点的全量恢复；

OceanBase 数据库的恢复支持在同集群内恢复，也支持在不同的集群内恢复；恢复会先根据用户输入的命令，从备份目的端将需要的宏块数据从对应的全量备份和增量备份中恢复到目标租户，并同步拉取和回放事务日志；

#### 1 前提条件

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base/ocean-base/ocean-base/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



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


#### 2 操作步骤

```sql
-- 恢复到指定时间戳：
ALTER SYSTEM RESTORE dest_tenant_name FROM uri UNTIL TIME='timestamp' WITH 'restore_option' [WITH KEY FROM 'backup_key_path' ENCRYPTED BY 'password'] [DESCRIPTION description];
-- 恢复到指定 SCN：
ALTER SYSTEM RESTORE dest_tenant_name FROM uri UNTIL SCN=scn WITH 'restore_option' [WITH KEY FROM 'backup_key_path' ENCRYPTED BY 'password'] [DESCRIPTION description];
-- 恢复到最新位点：
ALTER SYSTEM RESTORE dest_tenant_name FROM uri WITH 'restore_option' [WITH KEY FROM 'backup_key_path' ENCRYPTED BY 'password'] [DESCRIPTION description];
/*
dest_tenant_name：待恢复的新租户的名称；
uri：需要分别指定数据备份路径和日志归档路径；`UNTIL [TIME|SCN]` 指用户指定的恢复终点，恢复到该位点为止，且包括该位点。
	当指定恢复到 `TIME` 或 `SCN` 时, 必须以 `=` 来连接指定的值。
restore_option：
	支持指定 `pool_list`、`locality`、`primary_zone`、`concurrency`, 不同参数之间通过 `&` 分隔；
		在指定 `locality` 和 `primary_zone` 时，建议尽量与源租户保持同构。如果不同构，待租户恢复后激活为主租户可能会产生负载均衡操作，影响性能；
	pool_list`：填写为待恢复的新租户创建的资源池。多个资源池之间用英文逗号（,）分隔。
    Locality`：指定新租户副本分布的 Locality 信息，需要与新租户所在集群的 `pool_list` 的 Zone 信息相匹配。与源租户保持同构时，建议新租户与源租户的 F 副本个数相同。
    primary_zone`：指定新租户的 Leader 副本的偏好位置，需要与 `pool_list` 及 `locality` 相匹配。与源租户保持同构时，
	    建议新租户与源租户的第一优先级的 Primary Zone 个数相同；
    concurrency`：指定数据恢复的并发度。如果不指定，则默认等于该租户被分配的 MAX_CPU 数。例如，本文档中，系统租户为待恢复的租户分配的 MAX_CPU 为 16
WITH KEY FROM 'backup_key_path' ENCRYPTED BY 'password'：指定加密租户的秘钥备份信息。仅当源租户配置了透明加密，才需要在恢复时指定秘钥备份相关的信息。
	backup_key_path：秘钥的备份路径。
    password：备份秘钥时设置的加密密码。
*/

-- 阿里云 OSS：
	-- 从 OSS 备份路径与日志归档路径恢复租户 mysql 到指定时间戳 2020-06-01 00:00:00, 指定资源池为 restore_pool，同时恢复源租户的密钥信息。
	ALTER SYSTEM RESTORE mysql FROM 'oss://oceanbase-test-bucket/backup/data/?host=***.aliyun-inc.com&access_id=***&access_key=***,oss://oceanbase-test-bucket/backup/archive/?host=***.aliyun-inc.com&access_id=***&access_key=***' UNTIL TIME='2020-06-01 00:00:00' WITH 'pool_list=restore_pool' WITH KEY FROM 'oss://oceanbase-test-bucket/data_backup_dest/key?host=***.aliyun-inc.com&access_id=***&access_key=***' ENCRYPTED BY '******';

	-- 从数据备份路径与日志归档路径恢复租户 mysql 到指定 scn 1658285759724047000, 指定资源池为 restore_pool。
	ALTER SYSTEM RESTORE mysql FROM 'oss://oceanbase-test-bucket/backup/data/?host=***.aliyun-inc.com&access_id=***&access_key=***,oss://oceanbase-test-bucket/backup/archive/?host=***.aliyun-inc.com&access_id=***&access_key=***' UNTIL SCN=1658285759724047000 WITH 'pool_list=restore_pool';

	-- 从数据备份路径与日志归档路径恢复租户 mysql 恢复到当前最新的归档位点, 指定资源池为 restore_pool，同时指定恢复的并发度 concurrency 为 50。
	ALTER SYSTEM RESTORE mysql FROM 'oss://oceanbase-test-bucket/backup/data/?host=***.aliyun-inc.com&access_id=***&access_key=***,oss://oceanbase-test-bucket/backup/archive/?host=***.aliyun-inc.com&access_id=***&access_key=***' WITH 'pool_list=restore_pool&concurrency=50';

	-- 从数据备份路径与日志归档路径恢复租户 mysql 到指定时间戳 2020-06-01 00:00:00, 指定资源池为 restore_pool, 副本 Locality 为 F@z1,F@z2,F@z3。
	ALTER SYSTEM RESTORE mysql FROM 'oss://oceanbase-test-bucket/backup/data/?host=***.aliyun-inc.com&access_id=***&access_key=***,oss://oceanbase-test-bucket/backup/archive/?host=***.aliyun-inc.com&access_id=***&access_key=***' UNTIL TIME='2020-06-01 00:00:00' WITH 'pool_list=restore_pool&locality=F@z1,F@z2,F@z3';

	-- 从数据备份路径与日志归档路径恢复租户 mysql 到指定时间戳 2020-06-01 00:00:00, 指定资源池为 restore_pool, 副本 Locality 为 F@z1,F@z2,F@z3, 并指定 primary_zone 为 z1。
	ALTER SYSTEM RESTORE mysql FROM 'oss://oceanbase-test-bucket/backup/data/?host=***.aliyun-inc.com&access_id=***&access_key=***,oss://oceanbase-test-bucket/backup/archive/?host=***.aliyun-inc.com&access_id=***&access_key=***' UNTIL TIME='2020-06-01 00:00:00' WITH 'pool_list=restore_pool&locality=F@z1,F@z2,F@z3&primary_zone=z1';

-- NFS：
	-- 除 `url` 参数有所变更外, 其余参数的使用与 OSS 一致。
    -- 从数据备份路径与日志归档路径恢复租户 `mysql` 到指定时间戳 `2020-06-01 00:00:00`, 指定资源池为 `restore_pool`，同时恢复源租户的密钥信息。
    ALTER SYSTEM RESTORE mysql FROM 'file:///data/nfs/backup/data,file:///data/nfs/backup/archive' UNTIL TIME='2020-06-01 00:00:00' WITH 'pool_list=restore_pool' WITH KEY FROM 'file:///data_backup_dest/key' ENCRYPTED BY '******';
    
    -- 从数据备份路径与日志归档路径恢复租户 `mysql` 恢复到当前最新的归档位点, 指定资源池为 `restore_pool`，同时指定恢复的并发度 `concurrency` 为 `50`。
    ALTER SYSTEM RESTORE mysql FROM 'file:///data/nfs/backup/data,file:///data/nfs/backup/archive' WITH 'pool_list=restore_pool&concurrency=50';
 
-- 关于 腾讯云 COS，AWS S3，兼容 S3 协议的对象存储请查看官方文档；
```
有关各参数的详细说明请参见 [物理恢复相关参数介绍](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218386)，语句中 `TIME` 或 `SCN` 的选取以及各参数的详细说明请参见 [物理恢复相关参数介绍](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218386)，；

5. （可选）在待恢复租户对应的 Meta 租户创建成功后，如果需要加速恢复，可以通过配置项 `ha_high_thread_score` 配置恢复任务的后台调度权重；
```sql
-- 查看待恢复租户对应的 Meta 租户是否创建成功；
SELECT * FROM oceanbase.DBA_OB_TENANTS;
	-- 查询结果中，`TENANT_NAME` 列中以 `META$` 开头的租户即为 Meta 租户，当 Meta 租户的状态为 `TENANT_STATUS_NORMAL` 时，则表示 Meta 租户创建成功；
```

更多视图 `DBA_OB_TENANTS` 的字段说明信息请参见 [DBA_OB_TENANTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220071)，；

1. `ha_high_thread_score`：用于设置高可用高优先级线程的当前工作线程数；
	1. 默认值为 0，取值范围为 [0, 100]，建议修改为 10；
	2. 有关配置项 `ha_high_thread_score` 的更多介绍，请参见 [ha_high_thread_score](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220343)，；

```sql
-- 
ALTER SYSTEM SET ha_high_thread_score =10 TENANT = mysql;
```




### 参考文档



