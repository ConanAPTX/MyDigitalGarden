---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 发起日志备份/","dgPassFrontmatter":true}
---


#### 前提条件
必须完成备份前准备，[[15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OB 备份前准备\|OB 备份前准备]]；

#### 1. 操作步骤

##### 1. 配置备份模式，并开启归档日志压缩功能
该步骤为可选步骤，使用默认也是可以的；

备份模式目前支持 Optional 模式和 Mandatory 模式，如果不配置，默认为 Optional 模式：
-   Optional 模式表示以用户业务优先。在该模式下，当备份（日志归档）来不及的情况下，日志可能来不及备份就回收了，可能会发生备份断流；
    
-   Mandatory 模式表示以备份优先。在该模式下如果备份跟不上用户数据的写入，可能会导致用户无法写入；
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户

-- 2.配置备份模式，并开启归档日志压缩功能
obclient> ALTER SYSTEM SET backup_log_archive_option = 'optional compression= enable';
```

日志压缩目前支持的压缩算法有：zstd_1.3.8 和 lz4_1.0，默认使用压缩算法 lz4_1.0；
```sql
-- 可以根据业务需要动态修改压缩算法，您可以执行以下命令修改压缩算法
obclient> ALTER SYSTEM SET backup_log_archive_option='optional compression= zstd_1.3.8';
obclient> ALTER SYSTEM SET backup_log_archive_option='optional compression= lz4_1.0';
```


`配置后，如果需要关闭日志归档压缩:`
```sql
-- 对于当前备份模式为 Optional 模式的场景
ALTER SYSTEM SET backup_log_archive_option = 'compression= disable'; -- 命令直接关闭归档日志压缩功能，
ALTER SYSTEM SET backup_log_archive_option = 'mandatory compression= disable'; -- 配置备份模式并关闭归档日志压缩功能。
    
-- 对于当前备份模式为 Mandatory 模式的场景，
ALTER SYSTEM SET backup_log_archive_option = 'optional compression= disable'; --命令配置备份模式并关闭归档日志压缩
```

##### 2. 启动日志备份
为了减少日志备份发起的耗时，建议在开启日志备份前进行一次转储，待转储完成后再备份。这是因为日志备份的起始位点是最近一次转储位点，转储以后可以减少日志备份启动的时间；
```sql
-- 使用 `root` 用户登录数据库的 `sys` 租户
obclient> ALTER SYSTEM ARCHIVELOG;

```
启动成功后，OceanBase 数据库会自动将集群产生的事务日志定期备份到之前指定的备份目的地；


##### 3. 确认日志备份任务是否已开始

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base///#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





### 查询数据备份进度及备份任务历史
#### 1 查询数据备份进度以及备份任务历史

```sql
-- 查看备份进度，即查询正在备份的任务(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_PROGRESS;

-- 查看备份历史(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_SET_DETAILS;
```
更多 `CDB_OB_BACKUP_PROGRESS` 视图的说明，请参见 [CDB_OB_BACKUP_PROGRESS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945440) ，；
更多 `CDB_OB_BACKUP_SET_DETAILS` 视图的说明，请参见  [CDB_OB_BACKUP_SET_DETAILS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945439)，；


#### 2 通过视图查看日志的备份进度
```sql
-- 查看日志的备份进度(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
SELECT * FROM oceanbase.CDB_OB_BACKUP_ARCHIVELOG;
/*
当 `STATUS`为 `DOING`时，则表示日志备份任务已开始;
*/
```
更多 `CDB_OB_BACKUP_ARCHIVELOG` 视图的说明，请参见 [CDB_OB_BACKUP_ARCHIVELOG](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945458)，；


#### 3 通过 OCP 查看备份进度
详细情况：[查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；

### 参考文档
1. [查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；


</div></div>



##### 4.查看备份集中 Piece 的状态
```sql
obclient> SELECT * FROM CDB_OB_BACKUP_PIECE_FILES;
```
有关该系统视图中各个字段的详细解释，请参见 [CDB_OB_BACKUP_PIECE_FILES](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945464)，；


### 参考文档：
