---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/Server 管理及状态/","dgPassFrontmatter":true}
---


### Server 管理及状态

#### 1 检查 OBServer 状态
查询 OBServer 状态；查看 OBServer 状态；

##### 1.1 通过视图查看 OBServer 状态
所有的节点的 ip，对外提供的服务端口，内部通信端口，所属可用区，状态等信息；

```sql  
-- 查询集群内 OBServer 的状态(使用 root 用户登录到数据库的 sys 租户)  
obclient -h10.10.10.1 -P2881 -uroot@sys -p -A              -- 使用 root 用户登录数据库的 sys 租户

SELECT * FROM oceanbase.__all_server order by zone;  
SELECT id,svr_ip,svr_port,zone,stop_time,status FROM oceanbase.__all_server order by zone;  
SELECT id,svr_ip,svr_port,zone,inner_port,with_rootserver,status,block_migrate_in_time,start_service_time,stop_time,last_offline_time,build_version 
FROM oceanbase.__all_server 
order by zone;  
/* 字段详解：
gmt_create: ；  
gmt_modified:  
svr_ip: IP 地址；  
svr_port: 端口；  
id: 服务器 ID；  
zone: zone 名称；  
inner_port: 2881，sql 执行端口；  
with_rootserver: 是否 RS 所在服务器；  
status: 表示该 OBServer 状态；
	有3种状态；active，为正常状态；inactive，下线状态；deleting，正在被删除； 
block_migrate_in_time: 0  
build_version: 3.2.1_20211031212624-2c7eade2fd94a4ae32bec1683d1118da9d30cf8b(Oct 31 2021 22:03:03)  
stop_time：
	在该内部表中同时通过 stop_time 和 status 两个字段来标识 OBserver 的状态：  
	值为 0 时，表示 OBServer 为 started 状态；若值不为 0 时，表示 OBServer 处于 stopped 状态，且此时的值为 OBServer 被 Stop 的时间戳；  
start_service_time: 开始服务的时间；  
first_sessid: 0  
with_partition: 1  
last_offline_time: 最近下线时间； 
*/ 
```  

从 V4.0.0 版本开始引入 *oceanbase.DBA_OB_SERVERS* 视图，该视图用于展示所有 OBServer 节点的信息；
```sql
obclient> SELECT status FROM oceanbase.DBA_OB_SERVERS;
+--------+
| status |
+--------+
| active |
| active |
+--------+
2 rows in set (0.00 sec)
/* 字段详解：
status:
	如果返回结果为 `active`，说明 OBServer 处于正常运行状态；
	如果返回结果为 `inactive`，说明 OBServer 处于下线状态；
	如果返回结果为 `deleting`，说明 OBServer 处于正在被删除状态；
*/
```

##### 1.2 通过宿主机查看 OBServer 状态
```shell
# 1.登录 OBServer 所在的宿主机

# 2.在命令行工具中运行以下命令查看 observer 进程；
	# 查看OBServer进程：登录OceanBase Server 所在的宿主机
	ps -ef | grep observer
	root       6136      0 99 11:23 ?        08:53:50 ./bin/observer
	root      20514  19521  0 16:09 pts/25   00:00:00 grep --color=auto observer

# 启动 OBServer 进程：登录 OceanBase Server 主机
	cd /home/admin/oceanbase/
	./bin/observer [启动参数]
	./bin/observer --help   # 查看 OBServer 启动参数的详细信息

# 停止OBServer进程
	kill -15 `pgrepobserver`
	kill -9 `pgrepobserver`
```

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
2. *oceanbase.DBA_OB_SERVERS*：*SYS 租户*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820088)，；


