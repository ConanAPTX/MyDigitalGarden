---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v4.x/OceanBase 日志归档的准备工作_V4.2.1/","dgPassFrontmatter":true}
---



### OceanBase 日志归档的准备工作_V4.2.1
#### 1 （可选）配置归档并发度
开启归档模式前，您可以通过配置日志归档的并发度来提高租户的日志归档速度；

1. `log_archive_concurrency`：租户级配置项 `log_archive_concurrency` 用于配置日志归档总的工作线程数量；
	1. 该配置项为动态生效，不需要重启 OBServer 节点。该配置项的取值范围为 [0, 100]，默认值为 `0`，表示使用 OceanBase 数据库自适应的日志归档并行度。建议使用默认值；
	2. 注意：对于小规模租户（CPU 小于或等于 4C 的租户），建议使用默认值，不要调整该配置项的值；

```sql
-- 1.`sys` 租户或用户租户的租户管理员登录到数据库；


-- 2.选择合适的语句，设置配置项 `log_archive_concurrency`
	-- `sys` 租户调整指定租户的日志归档并发度
	ALTER SYSTEM SET log_archive_concurrency = 10 TENANT = mysql_tenant;

	-- `sys` 租户调整所有租户的日志归档并发度
	ALTER SYSTEM SET log_archive_concurrency = 10 TENANT = all_user;
	ALTER SYSTEM SET log_archive_concurrency = 10 TENANT = all;
		-- ceanBase 数据库从 V4.2.1 版本开始，TENANT = all_user 与 TENANT = all 语义相同，
			-- 在需要生效范围为所有用户租户时，推荐使用 TENANT = all_user，后续 TENANT = all 将废弃不再使用；

	-- 用户租户调整本租户的日志归档并发度
	ALTER SYSTEM SET log_archive_concurrency = 10;
```

有关配置项 `log_archive_concurrency` 的更多信息，请参见 [log_archive_concurrency](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220394)，；


#### 2 配置归档目的端
在开始日志归档任务前，您需要使用 `ALTER SYSTEM` 命令配置 `LOG_ARCHIVE_DEST` 参数。由于系统租户不包含用户数据，是集群管理用的租户，不支持备份恢复，故 `sys` 租户无需配置归档目的端；

配置归档目的端的操作主要是设置 `LOCATION`、`PIECE_SWITCH_INTERVAL` 和 `BINDING` 属性；

> [!NOTE] `注意事项`：
> 配置归档目的端时，要求每个租户的归档路径需要配置为独立的空目录，不同租户不能配置相同的归档路径；

##### 2.1 操作步骤
###### 2.1.1 配置归档目的端
```sql
-- 1.`sys` 租户或用户租户的租户管理员登录到数据库；
	-- 说明：MySQL 模式的管理员用户为 `root` 用户，Oracle 模式的管理员用户为 `SYS` 用户；

-- 2.配置归档目的端；
	-- 系统租户为指定租户配置归档目的端
	ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=archive_path [PIECE_SWITCH_INTERVAL=archive_mode] [BINDING=piece_switch_interval]' TENANT = tenant_name;

	-- 用户租户配置本租户的归档目的端
	ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=archive_path [PIECE_SWITCH_INTERVAL=archive_mode] [BINDING=piece_switch_interval]';
```


> [!NOTE] 注意：
> 对于从 OceanBase 数据库 V4.0.x 版本升级到 V4.1.0 版本的场景，版本升级后需要更改归档路径。对于从 OceanBase 数据库 V4.1.x 版本升级到 V4.2.x 版本的场景，版本升级后不需要更改归档路径。此外，从 OceanBase 数据库 V4.1.x 版本升级到 V4.2.x 版本时，支持升级期间进行日志归档；

1. `配置 LOCATION (必选)`：
*LOCATION* 属性用于指定归档目的端，目前 OceanBase 数据库支持的归档目的端介质除了 NFS 、阿里云 OSS 和腾讯云 COS，从 V4.2.1 BP7 版本开始，还支持 AWS S3 以及兼容 S3 协议的对象存储，例如：华为 OBS、Google GCS；


````tab
tab: 阿里云 OSS
使用 OSS 作为归档目的端介质时，除了设置归档路径、 `host`、`access_key` 和 `access_id`，您还可以通过 `delete_mode` 参数来配置归档文件的清理模式；

1. `delete_mode` 参数当前支持 `delete` 和 `tagging` 两种归档文件的清理模式，如果不配置，则默认为 `delete` 清理模式。
	1. `delete` ：表示清理模式为直接删除满足清理要求的归档文件；
	    配置为该模式后，当您通过自动方式清理归档文件时，对于满足清理要求的归档文件，系统会直接将其删除；
	    注意：该模式为默认的清理模式，如果不配置 `delete_mode` 参数，则默认使用 `delete` 清理模式；
	2. `tagging`：表示清理模式为对满足清理要求的归档文件设置 `Tag`，归档文件将仍然保留；
	配置为该模式后，当您通过自动方式清理归档文件时，对于满足清理要求的归档文件，系统会给这些文件设置标签，标签的 `key` 为 `"delete_mode"`，`value` 为 `"tagging"`，以便后续您可以通过设置的标签在 `OSS` 上对这些文件的生命周期进行管理；
    
```sql
-- 使用 OSS 作为归档介质时，系统租户为指定租户 `mysql_tenant` 设置归档目的端，并配置 `delete` 清理模式的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=oss://oceanbase-test-bucket/backup/archive?host=***.aliyun-inc.com&access_id=***&access_key=***&delete_mode=delete' TENANT = mysql_tenant;

-- 使用 OSS 作为归档介质时，用户租户为本租户设置归档目的端，并配置 `delete` 清理模式的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=oss://oceanbase-test-bucket/backup/archive?host=***.aliyun-inc.com&access_id=***&access_key=***&delete_mode=delete';
```

示例中，`oss://` 表示使用 OSS 作为归档目的端介质类型，存储桶名 `oceanbase-test-bucket`，在存储桶中的路径是 `/backup/archive`，同时使用 `?` 来分隔路径的其他参数，`host` 用于设置存储桶的主机地址，`access_id` 和 `access_key` 用于设置 `OSS` 的访问密钥，清理模式设置为 `delete`；

设置 `delete` 模式或 `tagging` 模式后，自动清理归档数据的相关操作请参见 [自动清理归档的数据](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218393)，；

tab: NFS
> [!NOTE] `注意`：
> 1. 使用 NFS 作为归档目的端时，需要注意以下事项：
> 	1. `LOCATION` 的值不支持设置为带有问号（?）的字符串；
> 	2. `LOCATION` 的值必须设置为绝对路径，保证 OBServer 服务器对 `LOCATION` 具有读写权限；
> 	3. 必须保证所有 OBServer 服务器都挂载了同一个服务器的 NFS。同时，为保证归档顺利进行，务必使用本文档中建议的参数挂载 NFS。挂载 NFS 的具体操作，请参见 [部署 NFS 客户端](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218107)，；

```sql
-- 使用 NFS 作为归档介质时，系统租户为指定租户 `mysql_tenant` 设置归档目的端的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=file:///data/nfs/backup/archive' TENANT = mysql_tenant;

-- 使用 NFS 作为归档介质时，用户租户为本租户设置归档目的端的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=file:///data/nfs/backup/archive';
```

示例中，`file://` 表示使用 NFS 作为归档目的端的介质类型，归档路径为 `/data/nfs/backup/archive`；

tab: 腾讯云 COS
腾讯云 COS

tab: AWS S3
AWS S3

tab: 兼容 S3 协议的对象存储
兼容 S3 协议的对象存储
````


2. `配置 BINDING 属性(可选)`：
*BINDING 属性* 用于设置归档和业务的优先模式；目前支持 *Optional 模式* 和 *Mandatory 模式*；如果不配置，默认为 *Optional 模式*；
*Optional 模式*：表示以用户业务优先。在该模式下，当归档（日志归档）速度跟上不日志生成的速度时，日志有可能来不及归档就被回收了，然后发生断流；
*Mandatory 模式*：表示以归档优先。在该模式下如果归档跟不上用户数据的写入，可能会导致用户无法写入；

```sql
-- 使用 NFS 作为归档介质时，系统租户为指定租户 `mysql_tenant` 配置归档路径并设置 `BINDING` 属性的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=file:///data/nfs/backup/archive BINDING=Optional' TENANT = mysql_tenant;

-- 使用 NFS 作为归档介质时，用户租户为本租户配置归档路径并设置 `BINDING` 属性的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=file:///data/nfs/backup/archive BINDING=Optional';
```

3. `配置 PIECE_SWITCH_INTERVAL 属性 (可选)`：
*PIECE_SWITCH_INTERVAL* 属性用于配置 piece 的切换周期，取值范围为 \[1d, 7d\]。如果不设置，默认为 `1d`；

```sql
-- 使用 NFS 作为归档介质时，系统租户为指定租户 `mysql_tenant` 配置归档路径并设置每隔一天切分一个日志 `piece` 的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=file:///data/nfs/backup/archive BINDING=Optional PIECE_SWITCH_INTERVAL=1d' TENANT = mysql_tenant;

-- 使用 NFS 作为归档介质时，用户租户为本租户配置归档路径并设置每隔一天切分一个日志 `piece` 的示例如下：
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST='LOCATION=file:///data/nfs/backup/archive BINDING=Optional PIECE_SWITCH_INTERVAL=1d';
```

###### 2.1.2 查询详细的参数设置信息
配置成功后，您可以通过视图查询详细的参数设置信息；
`sys` 租户可以通过 `CDB_OB_ARCHIVE_DEST` 视图来查看参数设置信息；用户租户可通过 `DBA_OB_ARCHIVE_DEST` 视图来查看参数设置信息。查看参数设置的详细操作，请参见 [查看归档参数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218402)，；

##### 2.2 配置后的注意事项及说明
参数 `LOG_ARCHIVE_DEST` 设置成功后，默认系统会在配置的目的端所在的目录下创建一个 `format` 文件，用于校验备份目的端的有效性信息，确保目的端内数据的完整性。故，在配置日志归档的目的端时，需要注意以下事项：

-   如果 `format` 文件不存在，则要求配置的目的端所在的目录为空，配置项才能设置成功。否则，系统会报 `-9080` 的错误，提示 format 文件不存在；
-   如果 `format` 文件已存在，则要求 `format` 文件的内容检验通过，配置项才能设置成功。否则，系统会报 `-9081` 的错误，提示 format 文件不匹配，format 文件的内容校验主要是检查集群、租户与备份目的端类型等与当前操作的集群、租户及备份目的端类型是否匹配；
-   在执行备份任务时，如果 `format` 文件不存在或 `format` 文件校验不通过，任务会发起失败；

此外，归档目的端配置成功后，不支持增量配置。例如，假设 NFS 下归档路径 `/data/nfs/backup/archive` 配置的 `BINDING` 属性为 `Mandatory`，`PIECE_SWITCH_INTERVAL` 属性为 `1d`。若需要更新 `PIECE_SWITCH_INTERVAL` 为 `2d`，但保留 `BINDING` 属性的配置 `Mandatory`，您仍然需要在命令中指定其他属性的值，否则未指定的属性将使用默认值；

您需要重新执行以下语句进行修改：
```sql
-- 系统租户修改指定租户
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST = 'LOCATION=file:///data/nfs/backup/archive BINDING=Mandatory PIECE_SWITCH_INTERVAL=2d' TENANT = mysql_tenant;

-- 用户租户修改本租户
obclient> ALTER SYSTEM SET LOG_ARCHIVE_DEST = 'LOCATION=file:///data/nfs/backup/archive BINDING=Mandatory PIECE_SWITCH_INTERVAL=2d';
```

#### 3 （可选）配置归档延迟
OceanBase 数据库使用租户级配置项 `archive_lag_target` 来控制租户日志归档的延迟时间，该配置项支持以毫秒(ms)、秒（s）、分（m）、小时（h）为单位，默认为 2 分钟，用于指定前后两次归档 IO 的最大间隔时间，确保在线日志能及时地被归档出去，从而减少数据损失的风险；

OceanBase 数据库的归档以日志流为单位，如果当前日志流有日志写入，并且距离该日志流上次归档的时间超过了配置项 `archive_lag_target` 所指定的时间间隔，该日志流就会触发一次归档动作，将未归档的日志都归档出去；

例如，如果配置项被设置为 `archive_lag_target='120s'`（即 2 分钟），则每一个日志流会尝试每 2 分钟进行一次归档动作（除非其他条件被满足，例如，日志归档的缓存聚合 Buffer 被写满），让该日志流的本次归档动作距离上次归档时间小于 2 分钟。需要注意的是，当配置项 `archive_lag_target` 的值设置为 0 时，将会达到一个准实时归档的效果；

有关配置项 `archive_lag_target` 的更多说明，请参见 [archive_lag_target](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220362)，；

> [!NOTE] 注意事项：
> 建议将配置项 `archive_lag_target` 设置为一个合理的值，以免过于频繁的 IO 影响系统性能，特别是使用对象存储的情况下。同时，过高的值也可能无法满足数据恢复的时效性，因此需要根据具体的业务需求和归档介质的性能来权衡 RPO；

```sql
-- 1.`sys` 租户或用户租户的租户管理员登录到数据库；

-- 2.根据实际情况，选择合适的语句，配置归档延迟；
	-- 系统租户为指定租户配置归档延迟时间
	obclient> ALTER SYSTEM SET archive_lag_target = '120s' TENANT = mysql_tenant;

	-- 系统租户为所有用户租户配置归档延迟时间
	obclient> ALTER SYSTEM SET archive_lag_target = '120s' TENANT = all_user;

	-- 用户租户为本租户配置归档延迟时间
	obclient> ALTER SYSTEM SET archive_lag_target = '120s';
```




### 参考文档



