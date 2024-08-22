---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v4.x/查询 OceanBase 日志归档信息_V4.x/","dgPassFrontmatter":true}
---


### 查询 OceanBase 日志归档信息_V4.x
#### 1 查看 Piece 信息
从 V4.0.0 版本开始引入 *CDB_OB_ARCHIVELOG_PIECE_FILES*，*DBA_OB_ARCHIVELOG_PIECE_FILES*；开启日志归档后，可以通过视图查看 Piece 信息；

````tab
tab: 1.1 系统租户查看所有租户的所有 Piece 信息
##### 系统租户查看所有租户的所有 Piece 信息
`sys` 租户可以通过 `CDB_OB_ARCHIVELOG_PIECE_FILES` 视图查看当前集群中所有租户的所有 Piece 信息；更多信息，请参见：[CDB_OB_ARCHIVELOG_PIECE_FILES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219480)，；

```sql
-- 1.使用 root 用户登录集群的 sys 租户；

-- 2.执行以下语句，查看所有租户的所有 Piece 信息
obclient [(none)]> SELECT * FROM oceanbase.CDB_OB_ARCHIVELOG_PIECE_FILES\G
```

tab: 1.2 用户租户查看本租户所有的 Piece 信息
##### 用户租户查看本租户所有的 Piece 信息
用户租户可以通过 `oceanbase.DBA_OB_ARCHIVELOG_PIECE_FILES` 视图（MySQL 模式）和 `sys.DBA_OB_ARCHIVELOG_PIECE_FILES` 视图（Oracle 模式）查看本租户的所有 Piece 信息；

```sql
-- 1.用户租户的租户管理员登录到数据库。
    -- 说明：MySQL 租户的管理员用户为 root 用户，Oracle 租户的管理员用户为 SYS 用户；
    
-- 2.执行以下语句，查看当前租户的所有 Piece；
    -- MySQL 租户查看本租户所有 Piece 的示例如下：
    obclient [(none)]> SELECT * FROM oceanbase.DBA_OB_ARCHIVELOG_PIECE_FILES\G
```
````


#### 2 查看归档进度
从 V4.0.0 版本开始引入 *CDB_OB_ARCHIVELOG*，*DBA_OB_ARCHIVELOG*；开启归档之后，您可以通过视图查看归档进度；

##### 2.1 系统租户查看
系统租户查看集群中所有租户的归档进度
sys 租户可以通过 `oceanbase.CDB_OB_ARCHIVELOG` 视图查看集群中所有租户的归档进度；
```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；
 
-- 2.执行以下语句，查看日志归档进度；
obclient [(none)]> SELECT * FROM oceanbase.CDB_OB_ARCHIVELOG\G
```
更多视图的说明：[[15_OceanBase/99_内部表介绍/日志归档信息_001#2 展示每个路径上的日志归档状态\|日志归档信息_001#2 展示每个路径上的日志归档状态]]，；

##### 2.2 用户租户查看
用户租户查看当前租户的归档进度
用户租户可以通过 `oceanbase.DBA_OB_ARCHIVELOG` 视图（MySQL 模式）和 `sys.DBA_OB_ARCHIVELOG` 视图（Oracle 模式）查看本租户的归档进度；
```sql
-- 1.租户管理员登录到数据库。
    -- 说明：MySQL 租户的管理员用户为 `root` 用户，Oracle 租户的管理员用户为 `SYS` 用户；

-- 2.执行以下语句，查看当前租户的日志归档进度。
    -- MySQL 租户查看本租户所有 Piece 的示例如下：
    obclient [(none)]> SELECT * FROM oceanbase.DBA_OB_ARCHIVELOG\G
```
更多视图的说明：[[15_OceanBase/99_内部表介绍/日志归档信息_001#2 展示每个路径上的日志归档状态\|日志归档信息_001#2 展示每个路径上的日志归档状态]]，；


#### 3 查看归档历史
可以通过视图查看归档的历史信息，包括当前正在进行的归档信息；

##### 3.1 系统租户查看
系统租户查看集群中所有租户的归档历史；
`sys` 租户可以通过 `oceanbase.CDB_OB_ARCHIVELOG_SUMMARY` 视图查看集群中所有租户的归档历史信息；
```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；
    
-- 2.执行以下语句，查看集群所有租户的归档历史信息
obclient [(none)]> SELECT * FROM oceanbase.CDB_OB_ARCHIVELOG_SUMMARY\G
```
更多信息请参见：[[15_OceanBase/99_内部表介绍/日志归档信息_001#3 展示历史以及当前所有的日志归档的状态\|日志归档信息_001#3 展示历史以及当前所有的日志归档的状态]]，；

##### 3.2 用户租户查看
用户租户查看当前租户的归档历史；
用户租户可以通过 `oceanbase.DBA_OB_ARCHIVELOG_SUMMARY` 视图（MySQL 模式）和 `sys.DBA_OB_ARCHIVELOG_SUMMARY` 视图（Oracle 模式）查看本租户的归档历史；
```sql
-- 1.用户租户的租户管理员登录到数据库。
	-- 说明：MySQL 租户的管理员用户为 `root` 用户，Oracle 租户的管理员用户为 `SYS` 用户；
    
-- 2.执行以下语句，查看本租户的日志归档历史；
    -- MySQL 租户查看本租户日志归档历史的示例如下：
    obclient [(none)]> SELECT * FROM oceanbase.DBA_OB_ARCHIVELOG_SUMMARY\G
```
更多信息请参见：[[15_OceanBase/99_内部表介绍/日志归档信息_001#3 展示历史以及当前所有的日志归档的状态\|日志归档信息_001#3 展示历史以及当前所有的日志归档的状态]]，；


#### 4 查看归档参数
日志归档过程中，您可以查看归档相关的参数配置信息；

````tab
tab: 4.1 系统租户查看租户的归档参数
##### 系统租户查看租户的归档参数
###### 查看所有租户的 LOG_ARCHIVE_DEST 参数；

```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；

-- `sys` 租户可以通过`CDB_OB_ARCHIVE_DEST` 视图查看集群中所有租户  `LOG_ARCHIVE_DEST` 参数的配置信息。
obclient [(none)]> SELECT * FROM oceanbase.CDB_OB_ARCHIVE_DEST;
+-----------+---------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+
| TENANT_ID | DEST_NO | NAME                  | VALUE                                                                                                                   |
+-----------+---------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+
|      1002 |       0 | binding               | OPTIONAL                                                                                                                |
|      1002 |       0 | dest_id               | 1002                                                                                                                       |
|      1002 |       0 | path                  | file:///data/nfs/backuparchive                                                                               |                
|      1002 |       0 | piece_switch_interval | 1d                                                                                                                      |
|      1002 |       0 | state                 | ENBALE                                                                                                                  |
|      1004 |       0 | binding               | Mandatory                                                                                                               |
|      1004 |       0 | dest_id               | 1001                                                                                                                       |
|      1004 |       0 | path                  | oss://oceanbase-test-bucket/backup/archive?host=xxx.aliyun-inc.com&access_id=xxx&access_key=xxx&delete_mode=tagging     |
|      1004 |       0 | piece_switch_interval | 1d                                                                                                                      |
|      1004 |       0 | state                 | ENBALE                                                                                                                  |
+-----------+---------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+
10 rows in set
```
更多 `CDB_OB_ARCHIVE_DEST` 视图的说明，请参见 [CDB_OB_ARCHIVE_DEST](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219468)，；

###### 查看租户的日志归档并行度；
租户的日志归档并行度由租户级配置项 `log_archive_concurrency` 控制，该配置项的取值范围为 [0, 100]，默认值为 `0`，表示使用 OceanBase 数据库自适应的日志归档并行度。有关配置项 `log_archive_concurrency` 的详细说明，请参见 [log_archive_concurrency](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220394)，；


```sql
obclient [(none)]> SHOW PARAMETERS LIKE 'log_archive_concurrency';
+-------+----------+----------------+----------+-------------------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------+--------+---------+-------------------+
| zone  | svr_type | svr_ip         | svr_port | name                    | data_type | value | info                                                                                                                                                                                                                                                                                                                               | section    | scope  | source  | edit_level        |
+-------+----------+----------------+----------+-------------------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------+--------+---------+-------------------+
| zone1 | observer | xxx.xx.xxx.198 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone3 | observer | xxx.xx.xxx.197 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone2 | observer | xxx.xx.xxx.196 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone2 | observer | xxx.xx.xxx.192 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone1 | observer | xxx.xx.xxx.194 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
+-------+----------+----------------+----------+-------------------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------+--------+---------+-------------------+
5 rows in set
```

tab: 4.2 用户租户查看本租户的归档参数

##### 用户租户查看本租户的归档参数
###### 查看本租户的 LOG_ARCHIVE_DEST 参数
用户租户可以通过 `oceanbase.DBA_OB_ARCHIVE_DEST` 视图（MySQL 模式）和 `SYS.DBA_OB_ARCHIVE_DEST` 视图（Oracle 模式）查看本租户 `LOG_ARCHIVE_DEST` 参数的配置信息；

```sql
-- 1.租户管理员登录到数据库；
	-- 说明：MySQL 租户的管理员用户为 `root` 用户，Oracle 租户的管理员用户为 `SYS` 用户；

-- MySQL 租户查看本租户的`LOG_ARCHIVE_DEST` 参数的示例如下
SELECT * FROM oceanbase.DBA_OB_ARCHIVE_DEST;
+---------+-----------------------+---------------------------------+
| DEST_NO | NAME                  | VALUE                           |
+---------+-----------------------+---------------------------------+
|       0 | binding               | OPTIONAL                        |
|       0 | dest_id               | 1002                            |
|       0 | path                  | file:///data/nfs/backup/archive |
|       0 | piece_switch_interval | 1d                              |
|       0 | state                 | ENBALE                          |
+---------+-----------------------+---------------------------------+
6 rows in set
```

###### 查看租户的日志归档并行度
租户的日志归档并行度由租户级配置项 `log_archive_concurrency` 控制，该配置项的取值范围为 [0, 100]，默认值为 `0`，表示使用 OceanBase 数据库自适应的日志归档并行度。有关配置项 `log_archive_concurrency` 的详细说明，请参见 [log_archive_concurrency](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220394)，；

```sql
SHOW PARAMETERS LIKE 'log_archive_concurrency';
+-------+----------+----------------+----------+-------------------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------+--------+---------+-------------------+
| zone  | svr_type | svr_ip         | svr_port | name                    | data_type | value | info                                                                                                                                                                                                                                                                                                                               | section    | scope  | source  | edit_level        |
+-------+----------+----------------+----------+-------------------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------+--------+---------+-------------------+
| zone1 | observer | xxx.xx.xxx.198 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone2 | observer | xxx.xx.xxx.196 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone3 | observer | xxx.xx.xxx.197 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
| zone1 | observer | xxx.xx.xxx.194 |     2882 | log_archive_concurrency | NULL      | 0     | log archive concurrency, for both archive fetcher and sender. If the value is default 0, the database will automatically calculate the number of archive worker threads based on the tenant specification, which is tenant max_cpu divided by 4; otherwise set the the worker count equals to the value.Range: [0, 100] in integer | LOGSERVICE | TENANT | DEFAULT | DYNAMIC_EFFECTIVE |
+-------+----------+----------------+----------+-------------------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------+--------+---------+-------------------+
4 rows in set
```
````


### 参考文档
1. *查看 Piece 信息*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218405)，；
2. *查看归档进度*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218396)，；
3. *查看归档历史*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218403)，；
4. *查看归档参数*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218402)，；



###  删除备份
````tab
tab: 2.1 系统租户查看集群中所有租户的归档进度
##### 系统租户查看集群中所有租户的归档进度
sys 租户可以通过 `oceanbase.CDB_OB_ARCHIVELOG` 视图查看集群中所有租户的归档进度；
```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；
 
-- 2.执行以下语句，查看日志归档进度；
obclient [(none)]> SELECT * FROM oceanbase.CDB_OB_ARCHIVELOG\G
```
更多 `CDB_OB_ARCHIVELOG` 视图的说明，请参见 [CDB_OB_ARCHIVELOG](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219536)，；

tab: 2.2 用户租户查看当前租户的归档进度
##### 用户租户查看当前租户的归档进度
用户租户可以通过 `oceanbase.DBA_OB_ARCHIVELOG` 视图（MySQL 模式）和 `sys.DBA_OB_ARCHIVELOG` 视图（Oracle 模式）查看本租户的归档进度；
```sql
-- 1.租户管理员登录到数据库。
    -- 说明：MySQL 租户的管理员用户为 `root` 用户，Oracle 租户的管理员用户为 `SYS` 用户；

-- 2.执行以下语句，查看当前租户的日志归档进度。
    -- MySQL 租户查看本租户所有 Piece 的示例如下：
    obclient [(none)]> SELECT * FROM oceanbase.DBA_OB_ARCHIVELOG\G
```
````


````tab
tab: 3.1 系统租户查看集群中所有租户的归档历史
##### 系统租户查看集群中所有租户的归档历史
`sys` 租户可以通过 `oceanbase.CDB_OB_ARCHIVELOG_SUMMARY` 视图查看集群中所有租户的归档历史信息；更多信息请参见：[CDB_OB_ARCHIVELOG_SUMMARY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219659)，；
```sql
-- 1.使用 `root` 用户登录集群的 `sys` 租户；
    
-- 2.执行以下语句，查看集群所有租户的归档历史信息
obclient [(none)]> SELECT * FROM oceanbase.CDB_OB_ARCHIVELOG_SUMMARY\G
```

tab: 3.2 用户租户查看当前租户的归档历史
##### 用户租户查看当前租户的归档历史
用户租户可以通过 `oceanbase.DBA_OB_ARCHIVELOG_SUMMARY` 视图（MySQL 模式）和 `sys.DBA_OB_ARCHIVELOG_SUMMARY` 视图（Oracle 模式）查看本租户的归档历史；
```sql
-- 1.用户租户的租户管理员登录到数据库。
	-- 说明：MySQL 租户的管理员用户为 `root` 用户，Oracle 租户的管理员用户为 `SYS` 用户；
    
-- 2.执行以下语句，查看本租户的日志归档历史；
    -- MySQL 租户查看本租户日志归档历史的示例如下：
    obclient [(none)]> SELECT * FROM oceanbase.DBA_OB_ARCHIVELOG_SUMMARY\G
```

````
