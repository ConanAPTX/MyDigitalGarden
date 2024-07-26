---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 数据备份备份前准备_V4.2.1/","dgPassFrontmatter":true}
---



### OceanBase 数据备份备份前准备

#### 1 数据备份架构

#### 2 配置备份目的端
配置备份目的端时，由于系统租户不包含用户数据，是集群管理用的租户，不支持备份恢复，故 `sys` 租户无需配置 `DATA_BACKUP_DEST` 参数；

##### 2.1 配置备份目的端
目前，OceanBase 数据库支持的备份介质除了 NFS、阿里云 OSS 和腾讯云 COS，从 V4.2.1 BP7 版本开始，还支持 AWS S3 以及兼容 S3 协议的对象存储，例如：华为 OBS、Google GCS；
```sql
-- 1.`sys` 租户或用户租户的租户管理员登录数据库。
	-- 说明：MySQL 模式的管理员用户为 `root` 用户，Oracle 模式的管理员用户为 `SYS` 用户；

-- 2.配置备份目的端
	-- 系统租户为指定租户配置备份目的端
	ALTER SYSTEM SET DATA_BACKUP_DEST= 'data_backup_path' TENANT = mysql_tenant;

	-- 用户租户配置本租户的备份目的端
	ALTER SYSTEM SET DATA_BACKUP_DEST= 'data_backup_path';
```

注意：对于从 OceanBase 数据库 V4.0.x 版本升级到 V4.1.0 版本的场景，版本升级后需要更改备份路径。对于从 OceanBase 数据库 V4.1.x 版本升级到 V4.2.0 版本的场景，版本升级后不需要更改备份路径；

详细设置说明如下：
````tab
tab: 阿里云 OSS
当使用 OSS 作为备份目的端时，除了设置备份目的端，您还可以通过 `delete_mode` 参数来配置备份文件的清理模式。`delete_mode` 参数当前支持 `delete` 模式和 `tagging` 模式。如果不配置 `delete_mode` 参数，则默认使用 `delete` 模式。有关 `delete_mode` 参数的详细说明，请参见 [查看数据备份相关参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218411)，；

使用 OSS 作为归档介质时，系统租户为指定租户 `mysql_tenant` 设置备份目的端并配置备份文件的清理模式的示例如下：
```sql
obclient> ALTER SYSTEM SET DATA_BACKUP_DEST='oss://oceanbase-test-bucket/backup/?host=***.aliyun-inc.com&access_id=***&access_key=***&delete_mode=delete' TENANT = mysql_tenant;

obclient> ALTER SYSTEM SET DATA_BACKUP_DEST='oss://oceanbase-test-bucket/backup/?host=***.aliyun-inc.com&access_id=***&access_key=***&delete_mode=delete';
```

示例中，`oss://` 表示使用 OSS 作为备份目的端介质类型，存储桶名 `oceanbase-test-bucket`，在存储桶中的路径是 `/backup`，同时使用 `?` 来分隔路径的其他参数，`host` 用于设置存储桶的主机地址，`access_id` 和 `access_key` 用于设置 `OSS` 的访问密钥，清理模式设置为 `delete`，；

设置 `delete` 模式或 `tagging` 模式后，自动清理备份数据的相关操作请参见 [自动清理过期备份](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218393)，；

tab: NFS
> [!NOTE] 注意：
> 1. 使用 NFS 作为备份目的端时，需要注意以下事项：
> 	1. `data_backup_dest` 的值不支持设置为带有问号（?）的字符串；
> 	2. `data_backup_dest` 的值必须设置为绝对路径，保证 OBServer 服务器对 `data_backup_dest` 具有写权限；
> 	3. 必须保证所有 OBServer 服务器都挂载了同一个服务器的 NFS。同时，为保证备份的顺利进行，务必使用本文档中建议的参数挂载 NFS。挂载 NFS 具体操作请参见 [部署 NFS 客户端](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218107)，；

```sql
-- 使用 NFS 作为备份介质时，系统租户为指定租户 `mysql_tenant` 设置备份目的端的示例如下：
obclient> ALTER SYSTEM SET DATA_BACKUP_DEST= 'file:///data/nfs/backup/data' TENANT = mysql_tenant;

-- 使用 NFS 作为备份介质时，用户租户为本租户设置备份目的端的示例如下：
obclient> ALTER SYSTEM SET DATA_BACKUP_DEST='file:///data/nfs/backup/data';
```

示例中，`file://` 表示使用 NFS 作为备份目的端的介质类型，备份路径为 `file:///data/nfs/backup/data`；

tab: 腾讯云 COS
内容二

tab: AWS S3
内容二

tab: 兼容 S3 协议的对象存储
内容二
````






##### 2.2 查看当前集群中各租户的备份路径等信息
配置成功后，`sys` 租户可通过视图 `CDB_OB_BACKUP_PARAMETER` 查看当前集群中各租户的备份路径等信息，具体操作请参见 [查看数据备份相关参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218411)，；


#### 3 备份秘钥
在进行数据备份前，还需要考虑源租户的加密情况，如果源租户配置了透明加密，则还需要为源租户备份秘钥；

> [!NOTE] `注意`：
> 备份秘钥后，在恢复数据前，如果源租户的加密信息触发过秘钥轮转，则需要重新备份源租户的秘钥；

```sql
-- 1.`sys` 租户或用户租户的租户管理员登录数据库；


-- 2.备份秘钥
	-- 系统租户为指定租户备份秘钥
	ALTER SYSTEM BACKUP KEY TENANT = tenant_name TO 'backup_key_path' ENCRYPTED BY 'password';

	-- 用户租户为本租户备份秘钥
	ALTER SYSTEM BACKUP KEY TO 'backup_key_path' ENCRYPTED BY 'password';

	/*
	参数：
		backup_key_path：指定秘钥待备份的路径。该路径由用户自定义，不能与数据备份或日志归档的路径相同；
	    password：为秘钥备份路径指定加密密码；
	*/

	--使用示例：
		-- 系统租户将租户 `mysql_tenant` 的秘钥备份到 `file:///data_backup_dest/key` 路径的示例如下：
		obclient [(none)]> ALTER SYSTEM BACKUP KEY TENANT = mysql_tenant TO 'file:///data_backup_dest/key' ENCRYPTED BY '******';
```


3. 配置成功后，可以通过视图查看秘钥备份的路径；

````tab
tab: 系统租户
```sql
-- 系统租户通过视图 CDB_OB_BACKUP_STORAGE_INFO 查看秘钥备份路径
SELECT * FROM oceanbase.CDB_OB_BACKUP_STORAGE_INFO;
```


tab: MySQL 模式
```sql
-- 用户租户通过视图 DBA_OB_BACKUP_STORAGE_INFO 查看秘钥备份路径
SELECT * FROM oceanbase.DBA_OB_BACKUP_STORAGE_INFO;
```

tab: Oracle 模式
```sql
-- 用户租户通过视图 DBA_OB_BACKUP_STORAGE_INFO 查看秘钥备份路径
SELECT * FROM SYS.DBA_OB_BACKUP_STORAGE_INFO;
```
````

有关视图中字段的详细说明，请参见： [CDB_OB_BACKUP_STORAGE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219520) ， [DBA_OB_BACKUP_STORAGE_INFO](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219577)，；
   


### 参考文档
1. *备份前准备*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218409)，；




