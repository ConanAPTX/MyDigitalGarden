---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v3.x/OB 发起日志备份/","dgPassFrontmatter":true}
---


#### 1 前提条件
必须完成备份前准备，[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 备份恢复_v3.x/OB 备份前准备\|OB 备份前准备]]，；

#### 2 操作步骤
##### 2.1 配置备份模式_可选步骤
*配置备份模式，并开启归档日志压缩功能*；该步骤为可选步骤，使用默认也是可以的；备份模式目前支持 `Optional 模式`和 `Mandatory 模式`，如果不配置，默认为 Optional 模式：

*Optional 模式*：表示以用户业务优先；在该模式下，当备份（日志归档）来不及的情况下，日志可能来不及备份就回收了，可能会发生备份断流；
*Mandatory 模式*：表示以备份优先；在该模式下如果备份跟不上用户数据的写入，可能会导致用户无法写入；
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.配置备份模式，并开启归档日志压缩功能
obclient> ALTER SYSTEM SET backup_log_archive_option = 'optional compression= enable';

-- 可以根据业务需要动态修改压缩算法，您可以执行以下命令修改压缩算法
obclient> ALTER SYSTEM SET backup_log_archive_option = 'optional compression= zstd_1.3.8';
obclient> ALTER SYSTEM SET backup_log_archive_option = 'optional compression= lz4_1.0';
```
日志压缩目前支持的压缩算法有：zstd_1.3.8 和 lz4_1.0，默认使用压缩算法 lz4_1.0；

`配置后，如果需要关闭日志归档压缩:`
```sql
-- 对于当前备份模式为 Optional 模式的场景：2个命令都可以，选择一个执行即可；
ALTER SYSTEM SET backup_log_archive_option = 'compression= disable';           -- 直接关闭归档日志压缩功能，
ALTER SYSTEM SET backup_log_archive_option = 'mandatory compression= disable'; -- 配置备份模式并关闭归档日志压缩功能。
    
-- 对于当前备份模式为 Mandatory 模式的场景，
ALTER SYSTEM SET backup_log_archive_option = 'optional compression= disable';  -- 配置备份模式并关闭归档日志压缩
```

##### 2.2 启动日志备份
为了减少日志备份发起的耗时，建议在开启日志备份前进行一次转储，待转储完成后再备份。这是因为日志备份的起始位点是最近一次转储位点，转储以后可以减少日志备份启动的时间；启动成功后，OceanBase 数据库会自动将集群产生的事务日志定期备份到之前指定的备份目的地；

```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 2.启动，停止日志备份
	SHOW PARAMETERS LIKE '%backup_dest%';    -- 查询日志备份相关配置项

	-- 转储数据，建议在开启日志备份前进行一次转储，待转储完成后再备份；
	obclient> alter system minor freeze;               

	obclient> ALTER SYSTEM ARCHIVELOG;                 -- 启动日志备份（若日志备份中断后，重新启动失败，可以先停止再启动）
		-- 若日志备份中断后，重新启动后，建议最后马上重新进行一次全量备份，因为中断后重新启动会导致之前的备份不可用；
	
```

##### 2.3 停止日志备份
```sql
-- 1.使用 `root` 用户登录数据库的 `sys` 租户

-- 停止日志备份任务
obclient> ALTER SYSTEM NOARCHIVELOG;               -- 停止日志备份任务(使用 root 用户登录数据库的 sys 租户) 
```



### 参考文档：
1. *发起日志备份*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945705)，；
2. [查看备份进度](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355896)，；