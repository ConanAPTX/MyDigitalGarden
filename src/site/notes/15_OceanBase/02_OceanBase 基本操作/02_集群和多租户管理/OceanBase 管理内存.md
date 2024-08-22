---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OceanBase 管理内存/","dgPassFrontmatter":true}
---


### OceanBase 管理内存
#### 1 内存管理概述  
OceanBase 数据库是一个支持多租户架构的准内存级的分布式数据库，这对大容量内存的管理和使用提出了很高的要求。*OceanBase 数据库采取占据服务器的大部分内存并进行统一管理的方式来管理内存*；  

当有新业务需要上线时为其创建一个新租户，其中内存是从租户可分配内存中划分资源，租户 CPU 和内存的大小取决于业务规模。当可分配的 CPU 和内存资源不足时，应水平扩展 OceanBase 集群，为不断扩展的业务规模提供可持续的服务能力；  

#### 2 数据库内存上限  
OceanBase 数据库提供 *memory_limit* 和 *memory_limit_percentage* 2个配置项来设置自身内存的上限，若 *memory_limit* 配置项设置有值，则该配置项生效； 若 *memory_limit* 参数值为 0 时，则 *memory_limit_percentage* 配置项生效；

目前主流的 OceanBase 数据库服务器一般内存为 384 GB 或 512 GB，384 GB 内存建议配置为使用机器内存的 80%，512 GB 内存建议配置为使用机器内存的 90%；  

若希望限制运行中的 OceanBase 数据库的内存大小，可以直接修改 *memory_limit* 的值，使其达到预期。设置后，后台参数 Reload 线程会使其动态生效，无需重启。但是在设置时，需要保证 *memory_limit* 的值小于系统总的值；


##### 2.1 相关配置项
1. `memory_limit`：用于设置 OBServer 可用的总内存大小；
<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



26. `memory_limit`：用于设置 OBServer 可用的总内存大小； 

</div></div>

	1. *memory_limit*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052603)，；
2. `memory_limit_percentage`：表示可用的总内存大小；
<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



27. `memory_limit_percentage`：用于设置系统总可用内存大小占总内存大小的百分比； 

</div></div>


```sql
-- 使用示例 1:
	-- memory_limit_percentage=80，memory_limit=0
	SHOW PARAMETERS LIKE 'memory_limit';
	SHOW PARAMETERS LIKE 'memory_limit_percentage';

	obclient> ALTER SYSTEM SET memory_limit = 0;
	obclient> ALTER SYSTEM SET memory_limit_percentage = 80;
		-- memory_limit 为 0，表示使用百分比的配置方式，故以 memory_limit_percentage 为准，OceanBase 数据库内存上限为 100 GB * 80% = 80 GB；  

-- 使用示例 2:
	-- memory_limit_percentage=80，memory_limit=90GB  
	SHOW PARAMETERS LIKE 'memory_limit';
	SHOW PARAMETERS LIKE 'memory_limit_percentage';

	obclient> ALTER SYSTEM SET memory_limit = 90GB;
	obclient> ALTER SYSTEM SET memory_limit_percentage = 80;
		-- memory_limit 为 90 GB，表示使用绝对值的配置方式，故以 memory_limit 为准，OceanBase 数据库内存上限为 90 GB；  
```


#### 3 系统内部内存管理  
虽然 OceanBase 数据库支持多租户架构，但是 OceanBase 数据库内存上限中配置的内存并不能全部分配给租户使用，因为每一个 OBServer 上租户都会共享部分资源或功能。由于这些共享的资源或功能所使用的内存并不属于任何一个普通租户，故这类内存被归结为`系统内部内存`；

`系统内部内存`可使用的上限可通过 *system_memory* 参数来配置，该参数是对系统内部使用内存的预估，实际上限制的是租户可使用的内存上限，即 *memory_limit - system_memory*；例如，假设 OceanBase 数据库内存上限为 80 GB，system_memory 的值为 20 GB，那么可用于租户分配的内存就是剩下的 60 GB； 

1. `system_memory`：用于设置系统预留给租户 ID 为 500 的租户的内存容量；
<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



28. `system_memory`：用于设置系统预留给租户 ID 为 500 的租户的内存容量； 

</div></div>


#### 4 租户内部内存管理  
每个租户内部的内存总体上分为两个部分：`不可动态伸缩的内存管理`，`可动态伸缩的内存管理`；

*不可动态伸缩的内存*：MemStore， MemStore 用来保存 DML 产生的增量数据，空间不可被占用；

*可动态伸缩的内存*：KVCache， KVCache 空间会被其它众多内存模块复用；
  
1. `MemStore`：大小由参数 *memstore_limit_percentage* 决定，表示租户的 MemStore 部分占租户总内存的百分比；
2. `KVCache`：
	1. 保存来自 SSTable 的热数据，提高查询速度；
	2. 大小可动态伸缩，会被其它各种 Cache 挤占；

##### 4.1 Memstore 内存管理相关配置项
1. `memstore_limit_percentage`：用于设置租户使用 MemStore 的内存占其总可用内存的百分比；
<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



31. `memstore_limit_percentage`：用于设置租户使用 MemStore 的内存占其总可用内存的百分比； 

</div></div>
  
	1. 详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944354)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000884899)，；
2. `freeze_trigger_percentage`：用于设置租户触发冻结内存的使用阈值；
<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



3. `freeze_trigger_percentage`：用于设置租户触发冻结内存的使用阈值； 

</div></div>

	1. *官方文档*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945013)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820928)，；

5. `writing_throttling_trigger_percentage`：用于设置写入速度的阈值；  
6. `write_throttling_maximum_duration`：通过控制内存分配进度，控制写入速度，即指定在触发写入限速后，剩余 memstore 内存分配完所需的时间；  


> [!NOTE] `关于 Memstore 内存管理的建议`：  
> 1. 为了防止 Memstore 因为转储速度跟不上写入速度导致的内存用满，建议设置 Memstore 的写入限流：  
> 	1. 把系统参数 *writing_throttling_trigger_percentage* 设置为 80 或 90，当 Memstore 的内存使用达到 80% 或者 90% 时来开启限流；  
> 	2. *writing_throttling_trigger_percentage* 的值一定要大于 *freeze_trigger_percentage*；  
> 2. 对于大规格租户，比如 32C 256G，可以适当调大 *memstore_limit_percentage* 的值，用于缓存更多的更新数据；  
> 3. 若业务中存在批量写入操作，比如批量导数的场景，可以调小 *freeze_trigger_percentage*，加快转储的频次，降低单次转储的数据量；


```sql
-- 通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'memstore_limit_percentage';
SHOW PARAMETERS LIKE 'freeze_trigger_percentage';

-- 修改转储配置
ALTER SYSTEM SET memstore_limit_percentage=50;        
ALTER SYSTEM SET freeze_trigger_percentage=20;



-- 通过 SHOW PARAMETERS 语句查询参数设置
SHOW PARAMETERS LIKE 'minor_compact_trigger';
SHOW PARAMETERS LIKE 'major_compact_trigger';

-- 修改转储配置
ALTER SYSTEM SET minor_compact_trigger=2;
ALTER SYSTEM SET major_compact_trigger=10;
```



### 参考文档



