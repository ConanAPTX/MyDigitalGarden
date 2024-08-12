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
###### 2.1.1 设置自身数据库内存上限方式  

1. OceanBase 数据库提供以下 2 种方式来设置自身内存的上限：
	1. 按照计算机器总内存上限的百分比计算自身可以使用的总内存，由 *memory_limit_percentage* 参数配置；  
	2. 直接设置 OceanBase 数据库可用内存的上限，由 *memory_limit* 参数配置。其中 *memory_limit* 参数值为 0 时，使用百分比的配置方式，否则使用绝对值的配置方式；  
	3. `memory_limit_percentage`：表示可用的总内存大小；
		1. 更多信息请参见：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍#^28c9d0\|OceanBase 配置项，变量详细介绍#^28c9d0]]，；
	4. `memory_limit`：用于设置系统总可用内存大小占总内存大小的百分比；
		1. 更多信息请参见：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍#^bb2a6b\|OceanBase 配置项，变量详细介绍#^bb2a6b]]，；

目前主流的 OceanBase 数据库服务器一般内存为 384 GB 或 512 GB，384 GB 内存建议配置为使用机器内存的 80%，512 GB 内存建议配置为使用机器内存的 90%；  

若希望限制运行中的 OceanBase 数据库的内存大小，可以直接修改 *memory_limit* 的值，使其达到预期。设置后，后台参数 Reload 线程会使其动态生效，无需重启。但是在设置时，需要保证 memory_limit 的值小于系统总的值；

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
	1. 更多说明信息请参见：[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/OB 配置管理/OceanBase 配置项，变量详细介绍#^6073ae\|OceanBase 配置项，变量详细介绍#^6073ae]]，；

#### 4 租户内部内存管理  
每个租户内部的内存总体上分为两个部分：`不可动态伸缩的内存管理`，`可动态伸缩的内存管理`；

*不可动态伸缩的内存*：MemStore， MemStore 用来保存 DML 产生的增量数据，空间不可被占用；

*可动态伸缩的内存*：KVCache， KVCache 空间会被其它众多内存模块复用；
  
1. `MemStore`：大小由参数 *memstore_limit_percentage* 决定，表示租户的 MemStore 部分占租户总内存的百分比；
	1. `memstore_limit_percentage`：用于设置租户使用 MemStore 的内存占其总可用内存的百分比；

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02/ob/ocean-base/#2db8ea" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



31. `memstore_limit_percentage`：用于设置租户使用 MemStore 的内存占其总可用内存的百分比； 

</div></div>

2. `KVCache`：
	1. 保存来自 SSTable 的热数据，提高查询速度；
	2. 大小可动态伸缩，会被其它各种 Cache 挤占；


### 参考文档



