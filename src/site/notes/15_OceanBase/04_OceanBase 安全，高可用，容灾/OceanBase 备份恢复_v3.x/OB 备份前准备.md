---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v3.x/OB 备份前准备/","dgPassFrontmatter":true}
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

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



#### 1 backup_dest_option 配置项


```sql
-- 1.使用 *root* 用户登录数据库的 *sys* 租户;

-- 查询配置项 
SHOW PARAMETERS LIKE 'backup_dest_option';

-- 2.在配置项 *backup_dest_option* 中添加 *log_archive_piece_switch_interval* 参数值的设置，开启日志分片的备份功能
ALTER SYSTEM SET backup_dest_option = 'log_archive_piece_switch_interval=1d';
ALTER SYSTEM SET backup_dest_option = 'log_archive_checkpoint_interval=2m&log_archive_piece_switch_interval=1d&backup_copies=0';
ALTER SYSTEM SET backup_dest_option = 'log_archive_checkpoint_interval=2m&log_archive_piece_switch_interval=1d&backup_copies=0';
ALTER SYSTEM SET backup_dest_option='log_archive_checkpoint_interval=2m&recovery_window=7d&auto_delete_obsolete_backup=true&log_archive_piece_switch_interval=1d&backup_copies=0';
	-- 配置冷分区 Checkpoint 任务的生成间隔为 2 分钟，开启切 Piece 模式且每天切分 1 个日志 Piece，7 天后自动清理过期的备份，清理时不考虑二次备份是否已成功；
	
```

1. `backup_dest_option`：用于控制备份相关的参数；【该配置项在 2.2.77 版本中新增】
2. `backup_dest_option`：该配置项用于控制备份相关的参数；
	1. 【*集群级*】【字符串类型】；该配置项默认值为：空字符串；动态生效；
	3. 有关该配置项的更多说明： [V2.2.77](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000375815)，[V3.1.2](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000375815)，[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944930)，；
	4. 通过配置该配置项可设置的备份参数如下：
		1. *log_archive_checkpoint_interval 参数* ：用于控制冷分区 Checkpoint 任务的生成间隔，以推进其归档进度；【*V 2.2.77 版本引入*】
			1. 该参数取值范围为 \[5s, 1h\]， 默认值为 120s；
			2. 说明：在日志备份过程中，有写入的分区由 Clog 被归档来推进其归档进度，由于冷分区没有日志写入，故需要引入 Checkpoint 任务来推进冷分区的归档进度；
			3. 该参数与配置项 *log_archive_checkpoint_interval* 的功能相同，建议使用配置项 *backup_dest_option* 来设置冷分区 Checkpoint 任务的生成间隔；
				1. 若 2 个配置项都未设置，则使用默认值 *120s*；若 2 个配置仅设置一个，则已设置的配置项生效；若 2 个配置项都已设置，则以 【*backup_dest_option*】 配置的值为准；
		2. *recovery_window 参数*：用于控制备份数据可恢复的时间窗口，以及作为判断备份数据是否过期的依据；【*V 3.1.2 版本引入*】
			1. 该参数取值范围为 \[0, +∞)，默认值为 0，表示备份数据不过期；单位为 d（天），填写值时，需带上单位才能生效；
			2. 通过 *recovery_window* 判断数据备份过期的方式： 比满足 `当前时间 - 备份时间点 >= recovery_window` 条件的最新备份更早的所有备份都被视为过期；
			3. 设置 *recovery_window=7d* 表示从当前时间往前推，7 天内的数据保证可恢复，且与这 7 天数据恢复无关的备份数据将被视为过期的数据；对于自动清理场景，备份的数据过期后，系统会按照一定的规则自动清理过期的数据；对于手动清理的场景，备份的数据过期后，管理员可以手动清理过期的备份数据
			4. 该参数与配置项 `backup_recovery_window` 的功能相同，建议使用配置项 *backup_dest_option* 来设置备份数据可恢复的时间窗口；
				1. 若 2 个配置项都未设置，则使用默认值 *0*，表示备份数据不过期；若 2 个配置仅设置一个，则已设置的配置项生效；
				2. 若 2 个配置项都已设置，则以 【*backup_dest_option*】 配置的值为准；
		3. *auto_delete_obsolete_backup 参数*：用于控制是否启动自动清理功能；【*V 3.1.2 版本引入*】
			1. 该参数取值范围为：true，false；默认为 *false*，表示系统不会自动清理过期的备份数据；若值为 true，则表示系统将会自动清理过期的备份数据；
			2. 自动清理场景下，设置该参数为 true，表示启动自动清理功能后，系统将会自动清理过期的备份数据；
			3. 该参数与配置项 `auto_delete_expired_backup` 的功能相同，建议使用配置项 *backup_dest_option* 来设置；
				1. 若 2 个配置项都未设置，则使用默认值 *false*，表示不自动清理过期的备份数据；若 2 个配置仅设置一个，则已设置的配置项生效；
				2. 若 2 个配置项都已设置，则以 【*backup_dest_option*】 配置的值为准；
		4. *log_archive_piece_switch_interval 参数*：用于控制自动按照时间段来切日志文件的目录；【*V 2.2.77 版本引入*】 			1. 该参数取值范围为： \[1d, 7d\]，默认值为 0，表示不切分 Piece；如果需要开启切分 Piece 的备份，则需要配置此参数；
			3. 例如，设置 *log_archive_piece_switch_interval=1d*，表示日志备份会每天切分一个日志 Piece，即从当前时间开始的 24 小时后，系统会自动开始切分下一个日志 Piece。受日志归档线程调度等其他因素的影响，下一次切分 Piece 的时间可能会有一定的延时；
			4. 配置项 *backup_dest_option* 中的 *log_archive_piece_switch_interval* 参数的配置及说明信息请参见 [备份前准备](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945704)，；
		5. *backup_copies 参数*：用于指定一个备份级的冗余度；【*V 2.2.77 版本引入*】
			1. 该参数取值范围为： \[0, 8\]，默认值为 0；如果设置了 *backup_backup_dest*，建议该参数设置为 *2*；
			2. 如果 *backup_copies* 设置为小于 *2*，则在清理备份时，系统不会考虑备份备份是否已成功；
			3. 如果 *backup_copies* 设置为 *2*，则仅当成功备份备份数据到 *backup_backup_dest* 后才会自动清理 *backup_dest* 下的备份。如果未设置备份备份，建议此参数使用默认值；



</div></div>




###  参考文档：



