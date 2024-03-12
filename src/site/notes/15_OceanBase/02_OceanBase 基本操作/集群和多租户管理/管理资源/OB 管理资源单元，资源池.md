---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/管理资源/OB 管理资源单元，资源池/","dgPassFrontmatter":true}
---


### OB 管理资源单元，资源池
#### 1 创建，修改，删除，查询资源单元

##### 1.1 创建资源单元
在创建租户前，需要先确定租户的资源单元配置和资源使用范围；可以通过 SQL 语句或 OCP 创建资源单元；
详细情况：[创建资源单元](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944863)，推荐使用 OCP 创建资源单元；

##### 1.2 修改资源单元的配置
修改资源单元配置即是调整资源单元配置中的 CPU、Memory 等的值；详细情况：[修改资源单元的配置](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946268)，；

##### 1.3 删除资源单元
可以删除不再使用的资源单元；详细情况：[删除资源单元](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946332)，*推荐通过 OCP 删除自定义的 Unit 规格*；

**注意**：使用 `DROP RESOURCE UNIT` 语句删除资源单元时，仅支持删除单个资源单元，不支持批量删除多个资源单元；


`资源单元未被使用`：如果资源单元未被指定给资源池，您可以直接执行以下语句，删除资源单元；
```sql
-- 资源单元未被使用
DROP RESOURCE UNIT unit1;   -- 资源单元未被指定给资源池，您可以直接执行以下语句，删除资源单元;
```


`资源单元正在被使用`：如果资源单元已被指定给资源池，需要为原资源池指定新的资源单元后，再删除资源单元；
```sql
-- 1.先创建资源单元 `unit2`，并 后，
CREATE RESOURCE UNIT unit2 MAX_CPU 4, MAX_MEMORY '5G', MAX_IOPS 128,MAX_DISK_SIZE '10G', MAX_SESSION_NUM 64, MIN_CPU=4, MIN_MEMORY= '5G', MIN_IOPS=128;

-- 2.修改资源池
ALTER RESOURCE POOL pool1 UNIT='unit2';   -- 将 `unit2` 指定给 `pool1`

-- 3.删除资源单元
DROP RESOURCE UNIT unit1;   -- 再删除 `unit1`
```

##### 1.4 查看资源单元配置
```sql
-- 
SELECT * FROM oceanbase.__all_unit;

-- 查看集群中所有的资源单元配置(使用 root 用户登录数据库的 sys 租户)
SELECT * FROM oceanbase.__all_unit_config;
SELECT * FROM oceanbase.__all_unit_config where unit_config_id=1031\G;

SELECT gmt_create,gmt_modified,unit_config_id,name,max_cpu,min_cpu,  
round(max_memory/1024/1024/1024,2) AS max_memory_gb,round(min_memory/1024/1024/1024,2) AS min_memory_gb ,max_iops,min_iops,max_disk_size,max_session_num  
FROM OceanBase.__all_unit_config;  
/*
查询结果中的相关字段说明如下:
	gmt_create: 资源单元的创建时间
	gmt_modified: 资源单元最近一次修改的时间
	unit_config_id: 资源单元的 ID
	name: 资源单元名称
	max_cpu: 该资源单元能够提供的 CPU 的上限，单位为核。
	min_cpu: 该资源单元能够提供的 CPU 的下限，单位为核。
	max_memory: 该资源单元能够提供的 Memory 的上限，单位为字节。
	min_memory: 该资源单元能够提供的 Memory 的下限，单位为字节。
	max_iops: 该资源单元能够提供的 IOPS 的上限，单位为字节。
	min_iops: 该资源单元能够提供的 IOPS 的下限，单位为字节。
	max_disk_size: 该资源单元能够提供的数据盘的最大容量，单位为字节。
	max_session_num: 该资源单元能够提供的最大会话数
*/
```

#### 2 创建，修改，删除，查询资源池
##### 2.1 创建，删除资源池


```sql

-- 1.创建资源池(使用 root 用户登录数据库的 sys 租户)  
create resource pool pl_5c2g unit=ut_2c2g, unit_num=1; -- 创建资源池 pl_5c2g  
CREATE RESOURCE POOL pool1 UNIT='unit1',UNIT_NUM=1,ZONE_LIST=('zone1','zone2','zone3'); -- 创建资源池 pool1 并为其指定资源配置

-- 2.删除资源池
-- 查看待删除的资源池 resource_pool1 是否被租户使用  
SELECT tenant_id,tenant_name FROM oceanbase.gv$unit WHERE resource_pool_name='resource_pool1';  
  
DROP RESOURCE POOL resource_pool1;   -- 删除资源池(使用 root 用户登录到集群的 sys 租户  )
  
-- 查看资源池  
SELECT * FROM oceanbase.__all_resource_pool where name='resource_pool1';
```


##### 2.2 

##### 2.3 查看资源池
```sql
-- 查看资源池
SELECT * FROM oceanbase.__all_resource_pool;

SELECT resource_pool_id,name,unit_count,unit_config_id,zone_list,tenant_id,replica_type,is_tenant_sys_pool
FROM oceanbase.__all_resource_pool 
WHERE tenant_id='1008';  -- 查看某个租户
/* 
oceanbase.__all_resource_pool 表部分字段介绍：
  resource_pool_id: 资源池ID；
  name: 资源池名称；
  unit_count: 即表示对应资源池中资源单元的个数；
  unit_config_id: 1033
  zone_list: zone3
  tenant_id: 租户ID；
  replica_type: 0
  is_tenant_sys_pool: 0
*/
```



### 参考文档：
1. [资源管理概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944862)，；