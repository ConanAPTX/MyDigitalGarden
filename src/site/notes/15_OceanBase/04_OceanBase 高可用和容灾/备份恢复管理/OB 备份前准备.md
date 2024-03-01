---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 备份前准备/","dgPassFrontmatter":true}
---


在进行备份前，需要配置备份目的端及备份相关的参数；

#### 1 配置备份目的端

目前，OceanBase 数据库支持 NFS 、阿里云 OSS 和腾讯云 COS 作为备份目的端；

##### 1.1 NFS

>`使用 NFS 作为备份目的端时，需要注意以下事项`：
>1. `backup_dest` 的值不支持设置为带有问号（?）的字符串；
>2. `backup_dest` 的值必须设置为绝对路径，保证 OBServer 对 `backup_dest` 具有写权限 ；
>3. 必须保证所有 OBServer 都挂载了同一个服务器的 NFS。同时，为保证备份的顺利进行，务必使用本文档中推荐的参数挂载 NFS。挂载 NFS 具体操作请参见 [部署 NFS 客户端](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944110)。
>4. 设置备份目的端并开启备份后，备份文件通常存放在以集群名命名的目录中，同一备份目的端可以存放不同集群的备份文件。如果在设置目的端时，备份目的端所在的目录中出现了与当前集群名相同的目录，则要求该目录（以集群名命名的目录）为空；

```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 设置备份目的端
obclient> ALTER SYSTEM SET backup_dest='file:///data/nfs/backup';
```


##### 1.2 阿里云 OSS
当使用 OSS 作为备份目的端时，除了设置备份目的端，您还可以通过 `delete_mode` 参数来配置备份文件的清理模式。
`delete_mode` 参数当前支持以下两种备份文件的清理模式：
- `delete`：表示清理模式为直接删除满足要求的备份文件。
配置为该模式后，当您通过自动或手动的方式清理备份文件时，对于满足清理要求的备份文件，系统会直接将其删除。该模式为默认的清理模式，如果不配置 `delete_mode` 参数，则使用默认清理模式 `delete`；
    
-   `tagging`：表示清理模式为对满足清理要求的备份文件设置 Tag，备份文件将仍然保留。
配置为该模式后，当您通过自动或手动的方式清理备份文件时，对于满足清理要求的备份文件，系统会给这些文件设置标签，标签的 `key` 为 `"delete_mode"`， `value` 为 `"tagging"`，以便后续您可以通过设置的标签在 OSS 上对这些文件的生命周期进行管理；
    
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 设置备份目的端
obclient> ALTER SYSTEM SET backup_dest='oss://oceanbase-test-bucket/backup/?host=xxx.aliyun-inc.com&access_id=xxx&access_key=xxx&delete_mode=delete';
```

> [!NOTE] 注意：
> 1.  `backup_dest` 设置完成且发起过备份后，将无法再修改 `delete_mode` 参数的值；
> 2. 禁止单独开启 OSS 的备份自动过期功能，必须配合OceanBase 数据库的 `delete_mode` 参数使用；


##### 1.3 腾讯云 COS

> [!NOTE] 在使用 COS 作为备份目的端时，需要注意以下事项：
> 1. 需要关闭 Bucket 的 List Cache，否则会导致备份数据一致性的错误。关闭 Bucket 的 List Cache 的相关操作请联系 COS 的技术支持人员协助处理；
> 2. 由于 COS 功能的限制，备份到 COS 的性能会比 OSS 差，同时可能存在 IO 读写放大的风险；
> 3. COS 暂不支持配置 `delete_mode` 参数；

```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 设置备份目的端
obclient> ALTER SYSTEM SET backup_dest='cos://oceanbase-test-appid/backup?host=cos.ap-nanjing.myqcloud.com&access_id=xxx&access_key=xxx&appid=xxx';
```

##### 1.4 华为云 OBS
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 设置备份目的端
obclient> ALTER SYSTEM SET backup_dest='obs//oceanbase-test-appid/backup?host=obs.cn-east-2.myhuaweicloud.com&access_id=xxx&access_key=xxx‘;
```


#### 2 配置备份参数
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户
ALTER SYSTEM SET backup_dest_option='log_archive_checkpoint_interval=2m&log_archive_piece_switch_interval=1d&backup_copies=0';
```

1. `backup_dest_option`：该配置项用于控制备份相关的参数；
	1. 【集群级配置项】，【字符串类型】，其默认值为：空字符串，参数修改后直接生效，不需要重启 OBServer；
	3. 通过配置该配置项可设置的备份参数如下：
		1. `log_archive_checkpoint_interval` 参数：
			1. 该参数用于控制冷分区 Checkpoint 任务的生成间隔，以推进其归档进度；
		3. `auto_delete_obsolete_backup=true` 参数：该参数用于控制是否启动自动清理功能；
			1. 自动清理场景下，设置该参数为 true，表示启动自动清理功能后，系统将会自动清理过期的备份数据；
		4. `recovery_window='7d'` 参数：该参数用于控制备份数据可恢复的时间窗口，以及作为判断备份数据是否过期的依据；
			1. 通过 `recovery_window` 判断数据备份过期的方式： 比满足 `当前时间 - 备份时间点 >= recovery_window` 条件的最新备份更早的所有备份都被视为过期；
		5. `log_archive_piece_switch_interval` 参数
			1. 参数用于控制自动按照时间段来切日志文件的目录；
			2. 默认值为 `0`，表示不切分 Piece, 有效范围为 [1d, 7d]。如果需要开启切分 Piece 的备份，则需要配置此参数；
		6. `backup_copies` 参数：
			1. 该参数用于指定一个备份级的冗余度，其默认值为 0；
	4. 详细情况请查看：[backup_dest_option](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944930)，；


###  参考文档：



