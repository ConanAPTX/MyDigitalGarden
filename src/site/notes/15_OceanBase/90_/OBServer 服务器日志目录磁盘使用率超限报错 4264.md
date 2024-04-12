---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/90_/OBServer 服务器日志目录磁盘使用率超限报错 4264/","dgPassFrontmatter":true}
---


### OBServer 服务器日志目录磁盘使用率超限报错 4264  
#### 1 适用版本  
OceanBase 数据库 V2.x、V3.x、V4.x 版本；  

#### 2 问题现象  
```shell
tail observer.log #  
  
df -Th #  
```  

#### 3 问题原因  
事务日志盘 /data/log1 包含 OceanBase 数据库的 redo log，而 redo log 主要包括 clog 和 ilog 两部分；  

clog 全称 commit log，位于 store/xxx/clog目录下，xxx 是集群名 ，记录数据库中数据所做的更改操作，提供数据持久性保证；ilog 全称 index log，位于 store/xxx/ilog目录下, 是 clog 的索引，本质上是一种优化，ilog 的文件删除不会影响数据持久性，但可能会影响系统的恢复时间；  

clog 日志盘的使用率如果超过了系统全局配置项 *clog_disk_usage_limit_percentage* 的值（默认值：95%）就会遇到如上告警和报错。这是一个刚性的限制，超过此值后该 OBServer 不再允许任何新事务的写入，同时不允许接收其他 OBServer 同步的日志。对外表现是所有访问此 OBServer 的读写事务报 transaction needs rollback 的错误；  

本质原因还是在于 clog 日志盘使用率达到 *clog_disk_utilization_threshold*（默认值：80%）后 clog 没有及时自动回收导致，该原因需要进一步分析处理；  

#### 4 解决方法  
##### 4.1 调整事务日志的磁盘 I/O 最大使用百分比  
clog 盘达到 95% 之后自动停写，无法再接收日志，该阈值由配置项 *clog_disk_usage_limit_percentage* 控制，可以调整该配置项的值为 98，临时调高该配置项的值，让 OceanBase 集群恢复正常的处理能力，同时也让 obclient/mysql 等客户端工具可以正常登录而不再 hang 住；  
  
```sql  
-- 1.若集群的 SYS 租户还可以正常连接登录，可以直接修改；
	-- 查询配置项的值  
	SHOW PARAMETERS LIKE 'clog_disk_usage_limit_percentage';  
	
	-- 查询该配置项的值，用于后面恢复该配置项的值；    
	ALTER system SET clog_disk_usage_limit_percentage = 98;  
	ALTER system SET clog_disk_usage_limit_percentage = 98 server ='[ip地址]:2882';  
		-- 可以指定只修改盘满的 server  

-- 2.若集群的 SYS 租户已经无法正常连接登录了；  
	/home/admin/oceanbase/bin/ob_admin -h 'xxx.xxx.xxx.xxx' -p 2882 set_server_config clog_disk_usage_limit_percentage=98  
		# 对应的 IP 地址为当前 OBServer 的 IP 地址；  
		# 该命令在 OceanBase 集群已经 hang 住的情况下也能生效；  
```   

##### 4.2 检查 clog 同步的情况  
修改之后，落后的副本会立即触发追日志；若落后很多会触发 rebuild(从 leader 拷贝 data+clog)；可以通过如下方式观察恢复过程；  

```powershell  
-- 执行如下 SQL 语句，检查 clog 不同步的分区数是否有不断减少  
SELECT svr_ip, count(*) FROM __all_virtual_clog_stat  
WHERE is_offline = 0 and is_in_sync = 0  
group by 1;  
```  
若没有快速减少，则可能有副本触发了 rebuild，继续执行如下 SQL 查询是否有正在做 rebuild 的副本；  
备注：rebuild 是一种落后太多情况下追赶的方式，会拷贝基线+增量；  
```powershell  
-- 查询是否有正在做 rebuild 的副本  
SELECT svr_ip, count(*) FROM __all_virtual_partition_migration_status  
WHERE action != 'END'  
group by 1;  
```  
观察上述查询结果是否在逐渐减小，如果是在减小，说明 rebuild 副本在进行中；  

##### 4.3 检查 clog 磁盘使用情况  
```powershell  
# 查询之前盘满的 server 的 clog 盘空间当前容量  
df -lh  
```  
若 clog 盘空间自动下降到 95% 以下，说明 OBServer 恢复顺利，继续等待所有副本达到 sync 状态即可；如果又达到了 98%，则说明这次调整没有恢复成功，请联系 OceanBase 技术支持人员处理；  

##### 4.4 将配置项修改回默认值  
```sql  
-- 查询配置项的值
SHOW PARAMETERS LIKE 'clog_disk_usage_limit_percentage'; -- 查询配置项的值  
  
alter system set clog_disk_usage_limit_percentage=98;  
```  


### 参考文档：  
1. [OBServer 服务器日志目录磁盘使用率超限报错 4264](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000207669?back=kb)，；  
2. [日志磁盘满](https://www.oceanbase.com/docs/community-observer-cn-10000000000450693)，；  
3. [修改 clog 磁盘占用百分比后 clog 未自动清理](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000267139?back=kb)，；






