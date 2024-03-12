---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OceanBase 集群资源池的分配，移除/","dgPassFrontmatter":true}
---


### 资源池的分配，移除  
#### 1 将资源池分配给租户  
资源池创建成功后，可以在创建租户时将资源池分配给租户，也可以在修改资源池列表时，将未使用的资源池分配给租户；  

##### 1.1 创建租户时分配资源池  
创建租户时，可将未被使用的资源池分配给新租户；  
注意事项：  
  
- 每个资源池仅能绑定给一个租户，每个租户可以拥有多个资源池。但是，在创建租户时，一个租户仅支持指定一个资源池；  
- 租户在 Zone 内被分配的所有资源总量为：Unit 规格 * Unit 数量；  
```sql  
CREATE TENANT  
IF NOT EXISTS test_tenant charset='utf8mb4',  
replica_num=3,  
zone_list=('zone1','zone2','zone3'),  
primary_zone='zone1;zone2,zone3',  
resource_pool_list=('pool1')  
```  
创建租户的更多信息请参见 [新建租户](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944834)；  

##### 1.2 修改租户资源池列表时分配资源池  
可以通过 ALTER TENANT 命令将未使用的资源池分配给租户；  
```sql  
/*  
语法：  
ALTER TENANT tenant_name RESOURCE_POOL_LIST [=](pool_name [, pool_name...]) ;  
语句使用说明：  
	该语句仅支持由 sys 租户的管理员执行。  
	对于 RESOURCE_POOL_LIST，一次仅支持添加或删除一个资源池。  
	为租户添加资源池时，待添加的资源池与原有资源池所分布的 Zone 不能有交集；  
*/ 
```  

##### 1.3 使用案例   
   在集群中增加 Zone 的具体操作请参见 [增加或删除 Zone](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946230)，向 Zone 内添加 OBServer 的具体操作请参见 [添加 OBServer](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946236)，Locality 的变更规则相关信息请参见 [Locality 概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944743)，；  

```sql  
-- 1.集群中当前仅有 z1、z2、z3 三个 Zone，需要在集群中增加 z4、z5 两个 Zone。

-- 2.在 z4、z5 两个 Zone 上各添加一台 OBServer。

-- 3.使用 root 用户登录 sys 租户，为租户 tenant1 在 z4、z5 上添加资源；
	-- 3.1 添加资源需要先有可用的资源单元和资源池 
	CREATE RESOURCE UNIT unit2 MAX_CPU 1, MAX_MEMORY '5G', MAX_IOPS 128,MAX_DISK_SIZE '10G', MAX_SESSION_NUM 64, MIN_CPU=1, MIN_MEMORY='5G', MIN_IOPS=128;  
	  
	CREATE RESOURCE POOL pool2 unit = 'unit2', unit_num = 1, zone_list=('z4','z5');  
	
	-- 3.2 完成后，为租户在 z4、z5 上添加资源池 
	ALTER TENANT tenant1 RESOURCE_POOL_LIST =('pool1', 'pool2');

-- 4.通过修改租户 Locality 来增加副本。
	-- 根据 Locality 的变更规则，每次只能增加一个 Zone 内的 Locality；
	ALTER TENANT tenant1 LOCALITY='F@z1,F@z2,F@z3,F@z4';  
	ALTER TENANT tenant1 LOCALITY='F@z1,F@z2,F@z3,F@z4,F@z5';  
```  
   

#### 2 从租户中移除资源池  
##### 2.1 从租户中移除资源池语法  
```sql  
/*  
语法：ALTER TENANT tenant_name RESOURCE_POOL_LIST [=](pool_name [, pool_name...]) ;  
语句使用说明：  
	该语句仅支持由 sys 租户的管理员执行。  
	对于 RESOURCE_POOL_LIST，一次仅支持删除一个资源池；
*/  
```  
  
##### 2.2 使用案例  
假设集群中当前有 z1、z2、z3、z4、z5 共 5 个 Zone，且 5 个 Zone 都属于同一个 Region，每个 Zone 内一台 OBServer。集群中有一个普通租户 tenant1，当前副本分布情况 locality='F@z1,F@z2,F@z3,F@z4,F@z5', resource_pool_list=('pool1','pool2')，资源池的资源分布情况 CREATE RESOURCE POOL pool1 UNIT 'uc1', UNIT_NUM 1, ZONE_LIST ('z1', 'z2', 'z3') 和 CREATE RESOURCE POOL pool2 UNIT 'uc2', UNIT_NUM 1, ZONE_LIST ('z4', 'z5')。  
根据业务需要，需要将租户 tenant1 由 5 副本调整为 3 副本，且资源池调整为 1 个，即租户的 Locality 变成 locality='F@z1,F@z2,F@z3,', resource_pool_list=('pool1')  
  
```sql  
-- 1.使用 root 用户登录集群的 sys 租户；  

-- 2.通过修改租户 tenant1 的 Locality 来删除副本；  
	-- 根据 Locality 的变更规则，每次只能删除一个 Zone 内的 Locality
	obclient>ALTER TENANT tenant1 LOCALITY='F@z1,F@z2,F@z3,F@z4';   
	obclient>ALTER TENANT tenant1 LOCALITY='F@z1,F@z2,F@z3';  

-- 3.删除 z4、z5 上的资源池 pool2
obclient>ALTER TENANT tenant1 RESOURCE_POOL_LIST =('pool1');
```  
Locality 的变更规则相关信息请参见 [Locality 概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944743)，；  

### 参考文档



