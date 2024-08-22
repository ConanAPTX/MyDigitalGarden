---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/OceanBase 单机，租户的分区数限制/","dgPassFrontmatter":true}
---



### OceanBase 单机，租户的分区数限制



#### 1 如何计算租户可用内存上限
对于 F 类型的 unit，需要为 memstore 预留内存：`租户可用内存上限` = *租户总内存上限   \*   (1 - memstore\_limit\_percentage)*
对于 L 类型的 unit，无需为 memstore 预留内存：`租户可用内存上限` = *租户总内存上限*

> [!NOTE] Title
> 若创建租户时的 *min_memory*，*max_memory* 不一致，则【租户总内存上限】为 *min_memory*；
> *memstore_limit_percentage* 为集群配置项；
> 

```sql
-- 查询每个 Server 的 `memstore_limit_percentage`
select svr_ip, svr_port, name, value from __all_virtual_sys_parameter_stat where name = 'memstore_limit_percentage;

SHOW PARAMETERS LIKE 'memstore_limit_percentage';
```


#### 2 V2.x 版本
对于 OceanBase V1.4.X 和 OceanBase V2.2.X 版本，建议单个节点的分区数目不超过 5 万，如果超过了这个数目，则建议对 OceanBase 集群进行扩容。对于单节点上分区数目限制的建议，主要是因为当一台物理机上的分区过多，会对系统造成以下影响：

- CLOG 主备 keep alive 的 RPC 开销增大
- 后台遍历分区的定时任务的开销增大；
- 每个分区会占用系统内存，降低 OceanBase 集群性能；
- 容灾场景下，系统恢复时间变长；


##### 2.1 计算租户的分区使用内存
对于 OceanBase 数据库 V2.X 版本，还对每台物理机上的分区数目做出了限制，计算公式如下：在 OceanBase 数据库 V2.1.1 及以前的版本中，由于没有使用分区组（Partition Group，以下简称 PG），假设租户当前分区总数为 num，根据如下计算公式进行评估：
$$
partition\_mem = 128 KB * num + max\{ 1000, num / 10 \} * 400 KB 
$$
其中： `partition_mem` 表示分区使用的内存，`num` 表示分区数量；
假如：租户分区数量为 13000，则计算方法如下：
```
partition_mem = 128KB * 13000 + max{1000, 13000/10} * 400KB 
	= 1664000 +  1300 * 400  
	= 1664000 + 520000 
	= 2184000 /1024/1024 = 2.06GB
```
查询 OceanBase 数据库分区情况：[[15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OceanBase  集群副本分布情况\|查询 OceanBase  集群副本分布情况]]，；

> [!NOTE] 说明：
> 1. 需要确保算出来的值不能超过租户可用内存上限，否则需调大租户内存规格；此外，还需要确保：
> 	1. num >= 1 万 时，同时有写入的分区比例不超过 10%；
> 	2. num < 1 万时，同时有写入的分区总数不超过 1000 个；


```sql
-- 统计每个租户在每个 Server 上的内存上限
select u.svr_ip, u.svr_port, t.tenant_id, uc.max_memory, p.replica_type 
from __all_unit u, __All_resource_pool p, __all_tenant t, __all_unit_config uc 
where p.resource_pool_id = u.resource_pool_id and t.tenant_id = p.tenant_id and p.unit_config_id = uc.unit_config_id;
```


##### 2.2 计算租户允许的最大分区数
单机租户允许创建的最大分区数量为  = *(max_memory - memstore_limit）/ partition_mem*

1. `partition_mem` 表示分区使用的内存；
2. `max_memory` 为创建租户时指定的最大内存；
3. `memstore_limit`：表示 memstore 能够使用的内存上限；其计算公式：`memstore_limit` = *memstore_limit_percentage*  *  *min_memory*；
	1. *memstore_limit_percentage*：；
	2. *min_memory*： 为创建租户时指定的最大内存；

##### 2.3 对于 V2.2.X 及以后的版本
对于 OceanBase 数据库 V2.2.X 及以后的版本，如果使用了 PG 功能，由于一个 PG 中可能包含很多分区，其内存开销要比普通的单个分区大，按每个 PG 预留 （528KB+1MB）内存来计算，根据系统已有的 PG 数量计算需要为 PG 预留的内存；


#### 3 OceanBase 3.x 版本
##### 3.1 OBServer 节点分区数限制 
OceanBase 数据库 V4.0 架构之前采用分区日志流，每一个分区是一个单独的 Paxos 组。当一台 OBServer 节点包含很多分区时，Clog 主备 Keep Alive 的 RPC 开销增大，后台遍历分区的定时任务的开销增大，每个分区占用的内存增加，整个 OBServer 节点的 CPU 开销会比较高，OBServer 节点的服务能力下降；容灾场景下，系统恢复时间也会过长，影响 RPO；

目前 3.x 版本的 OceanBase 数据库的单机 partition 上限已经增加到 50w；然而当单机 partition 分区数达到 5w 以后，由于 RS 内部事务调度线程压力和内部 Schema 刷新机制，节点重启恢复时间和 DDL 执行时间已经会比正常执行 RT 高很多，所以生产中仍建议以 OCP 默认单机 partition 上限告警阈值 3w 为推荐值；

查询 OceanBase 数据库分区情况：[[15_OceanBase/20_OceanBase 常用 Sql 语句/查询 OceanBase  集群副本分布情况\|查询 OceanBase  集群副本分布情况]]，；

> [!NOTE] 注意：
> - 对于分区表，每个分区均会算一次 partition 数；
> - 对于索引：如果是非本地索引，会独立算 partition 数；如果是本地索引，则不会额外增加分区数；


##### 3.2 租户分区数限制
除了单机上分区数限制以外，租户的最大分区数还受限于租户的资源规格。如果租户资源规格不够，在 OceanBase 数据库中创建分区的操作可能会失败，报 `ret=-5188（too many partitions）`的错误；

​租户在任一 OBServer 节点上的分区数上限是根据租户资源单元的内存上限来计算的，计算公式如下：
$$
partition\_num = (max\_memory - memstore\_limit）/ partition\_mem
$$
其中：
- max_memory：租户最大内存；
- memstore_limit：MemStore 内存；
- partition_mem：每个分区占用的内存（系统按这个大小来估算分区总数并加以限制）；
    - 不在 partition group 的分区：168K；
    - 在 partition group 中的分区：512K；


### 参考文档
1. [OceanBase 数据库为何对分区数有限制](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000098)，；
2. [租户分区数上限计算方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000990095?back=kb)，；
3. [OceanBase 3.x 版本单机分区数限制](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000207651?back=kb)，；
4. [OceanBase 在建表时报分区数超限？到底上限是多少？](https://open.oceanbase.com/blog/5554050304?_gl=1*1gpw834*_ga*MjQ4NTE2NTQ1LjE3MTYxODgxNzY.*_ga_T35KTM57DZ*MTcyNDA2MjEyNi4yMzMuMS4xNzI0MDYyODEwLjExLjAuMA.)，；



