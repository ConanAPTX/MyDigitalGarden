---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/Server 管理及状态/","dgPassFrontmatter":true}
---


### Server 管理及状态



黑屏：Observer 服务启动恢复
由于增删改数据在内存中，进程启动后：
1. 需要与其他副本同步，将clog或者ssd基线数据进行同步（补齐）
2. 需要将上一次合并之后的内存数据恢复出来（clog回放），才能提供服务；

注意事项：
1. 停机时间短（分钟或者小时级别），一般只追齐clog；
2. 停机时间长（天级别），clog落后太多，会直接追齐ssd基线数据，然后补齐合并版本后的clog
3. 这一过程结束后，该OBServer才能对外提供服务，可以查询 `__all_server` 表的 startservicetime 字段来判断 OBServer 是否对外提供服务；
4. 可以在停止 OBServer 服务前执行转储（alter system minor freeze;），以加快 OBServer 服务恢复过程；


黑屏：服务停止（停机运维）
机器需要运维操作时，需要停止 OceanBase 服务进程：
```sql
-- 1.系统租户登陆，确定运维时长，如果大于1小时但小于1天，为了避免服务恢复后的补副本操作，需要设置永久下线时间；
	alter system set server_permanent_offline_time = '86400s';

-- 2.将服务从当前 OBServer 切走，保证停服务的时候，对于业务没有影响；（）
	alter system stop server 'ip地址:2882';   -- 内含切主动作；

-- 3.检查主副本都切走
	-- 返回值应为 0
	select count(*) 
	from __all_virtual_tablet, __all_virtual_meta_tablem 
	where t.table_id=m.table_idand role = 1 and m.svr_ip = 'ip地址';

-- 4.停止进程
	kill -15 <observer pid>
```


黑屏：服务恢复（停机运维结束）
机器需要运维操作结束后，需要恢复 OceanBase 服务进程；
```shell
# 1.机器上电

# 2.检查该机器ntp同步状态和服务运行情况

# 3.admin 用户启动 OBServer 进程
	# 启动 OBServer 进程：登录 OceanBase Server 主机
	cd /home/admin/oceanbase/
	./bin/observer [启动参数]
	./bin/observer --help   # 查看 OBServer 启动参数的详细信息

# 4.系统租户登陆，启动 server
	alter system start server 'ip地址:2882';

# 5.检查__all_server表，查看status为'active' 且'start_service_time'的值>0，则表示OBServer正常启动并开始提供服务；

# 6.将永久下线时间改回默认值 3600s
	alter system set server_permanent_offline_time='3600s';
```


黑屏：故障节点替换
首先要确保集群中有足够的冗余资源（OBServer），可以代替故障节点进行工作：
```sql
-- 1.系统租户登陆，stopserver，确保主副本都切走

-- 2.为目标 zone 添加新的 server
	alter system add server 'ip地址:2882' ZONE 'zone1';

-- 3.将故障 server 下线，OB 会自动将被下线 OBServer 的 Unit 迁移至新添加的 OBServer 上；
	alter system delete server 'ip地址:2882' ZONE 'zone1';

-- 4.检查__all_server表检查server状态，旧OBServer的信息已经消失
```



#### 2 管理 OBServer状态 
```sql
-- 添加 Server（状态为 Inactive，随后切为 active[start_service_time>0]） 
ALTER SYSTEM ADD SERVER 'ip:port' [,'ip:port'…] [ZONE=’zone_name’]; 

-- 删除 Server（状态为 Deleting） 
ALTER SYSTEM DELETE SERVER 'ip:port' [,'ip:port'…] [ZONE=’zone_name’]; 

-- 取消删除 Server（状态为 Active[start_service_time>0或stop_time>0]） 
ALTER SYSTEM CANCEL DELETE SERVER 'ip:port' [,'ip:port'…] [ZONE=’zone_name’]; 

-- Start Server （状态为 Active[start_service_time>0且Active[stop_time=0]） 
ALTER SYSTEM START SERVER 'ip:port' [,'ip:port'...] [ZONE='zone']; 

-- Stop Server （状态为 Active[stop_time>0]） 
ALTER SYSTEM STOP SERVER 'ip:port' [,'ip:port'...] [ZONE='zone'];
```


![OB 集群 OBServer 状态_001.png|400](/img/user/02_%E9%99%84%E4%BB%B6/Image/15_OceanBase/OB%20%E9%9B%86%E7%BE%A4%20OBServer%20%E7%8A%B6%E6%80%81_001.png)



### 参考文档
1. [检查 OBServer 状态_V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218101)，；



