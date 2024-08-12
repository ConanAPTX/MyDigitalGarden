---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/分区表_Oracle 模式/","dgPassFrontmatter":true}
---


### 分区表_Oracle 模式
#### 1 分区表概述

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 分区表概述
OceanBase 数据库支持`非分区表`和`分区表`；

未定义分区方式的表称为*非分区表*，否则为分区表；*分区表*由一个或多个分区组成，这些分区是单独管理的，可以独立于其他分区运行。即使分区表仅由一个分区组成，该表也不同于未分区表，非分区表不能添加分区；

OceanBase 数据库把普通的表的数据按照一定的规则划分到不同的区块内，同一区块的数据物理上存储在一起。这种划分区块的表叫做`分区表`，其中的每一个区块称作分区；分区技术是非常重要的分布式能力之一，它能解决大表的容量问题和高并发访问时性能问题；

普通的表只有一个分区，可以看作分区表的特例。每个分区只能存在于一个节点内部，分区表的不同分区可以分散在不同节点上；

1. 创建分区的目的是为了在特定的SQL操作中减少数据读写的总量以减少响应时间：
	1. 可扩展性；
	2. 可管理性；
	3. 提高性能；


##### 1.1 OceanBase 分区表特点

1. 可多机扩展；
2. 提高可管理性；
3. 提高性能；
4. 自动负载均衡、自动容灾；
5. 对业务透明，可以取代“分库分表”方案；
6. 支持分区间并行；
7. 单表分区个数最大 8192；
8. 单机 partition 支持上限：8万（推荐不超过3万）；

##### 1.2 OceanBase 分区表的分区类型
1. OceanBase 分区表分为*一级分区* 和 *二级分区*
	1. OB 现在支持的一级分区类型有：
		1. *HASH*， *KEY*， *LIST*，*RANGE*，*RANGE COLUMNS*，*生成列分区*；
	2. *二级分区* 相当于在*一级分区* 的基础上，又从第二个维度进行了拆分：
		1. `MySQL 模式`：该模式支持分区；
			1. *RANGE 分区*，*RANGE COLUMNS 分区*，*LIST 分区*，*LIST COLUMNS 分区*，*HASH 分区*，*KEY 分区*，*组合分区*；
		2. `Oracle 模式`：该模式支持分区；
			1. *RANGE 分区*，*LIST 分区*，*HASH 分区*，*组合分区*；
	

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/oracle/oracle/#1-oracle" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 Oracle 模式支持的分区类型

1. OceanBase 数据库 Oracle 模式的一级分区支持的分区类型如下：
	1. *Range 分区*、*List 分区*，*Hash 分区*；
2. OceanBase 数据库 Oracle 模式的二级分区为任意两种分区类型的组合，具体支持情况如下：

| <p align="center">二级分区类型</p>  | 创建模板化二级分区表 | 创建非模板化二级分区表 | 备注 |
| :------------- | :----------: | :------------: |:------------ |
| Range + Range | 支持 | 支持 |  |
| Range + List | 支持 | 支持 |  |
| Range + Hash | 支持 | 支持 |  |
| List + Range | 支持 | 支持 |  |
| List + List | 支持 | 支持 |  |
| List + Hash | 支持 | 支持 |  |
| Hash + Range | 支持 | 支持 |  |
| Hash + List | 支持 | 支持 |  |
| Hash + Hash | 支持 | 支持 |  |



</div></div>


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/my-sql/#1-my-sql" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 MySQL 模式支持的分区类型

1. OceanBase 数据库 MySQL 模式一级分区支持的分区类型如下：
	1. *Range 分区*，*Range Columns 分区*，*List 分区*，*List Columns 分区*， *Hash 分区*，*Key 分区*；
2. OceanBase 数据库*二级分区* 为任意两种分区类型的组合，具体支持情况如下：

| <p align="center">二级分区类型</p>  | 模板化二级分区表 | 非模板化二级分区表 | 备注 |
| :------------- | :---------- | :------------ |------------ |
| Range + Range / Range + Range Columns / Range + List /<br> Range + List Columns / Range + Hash / Range + Key |   支持   | 支持 |  |
| Range Columns + Range / Range Columns + Range Columns / Range Columns + List /<br> Range Columns + List Columns / Range Columns + Hash / Range Columns + Key |   支持   | 支持 |  |
| List + Range / List + Range Columns / List + List /<br> List + List Columns / List + Hash / List + Key |   支持   | 支持 |  |
| List Columns + Range / List Columns + Range Columns / List Columns + List /<br> List Columns + List Columns / List Columns + Hash / List Columns + Key |   支持   | 支持 |  |
| Hash + Range / Hash + Range Columns / Hash + List /<br> Hash + List Columns / Hash + Hash / Hash + Key |   支持   | 支持 |  |
| Key + Range / Key + Range Columns / Key + List /<br> Key + List Columns / Key + Hash /Key + Key	支持	支持 |   支持   | 支持 |  |



</div></div>




</div></div>

#### 2 



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 参考文档
1. `MySql 模式`
	1. *V4.2.1：参考指南 > 系统原理 > 数据库对象 > MySql 模式 > 分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221003)，；
	2. *分区概述*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220228)，；
	3. *创建分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220224)，；
	4. *分区裁剪*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220226)，；
	5. *指定分区查询数据*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220231)，；
2. `Oracle 模式`
	1. *V4.2.1：参考指南 > 系统原理 > 数据库对象 > Oracle 模式 > 分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221034)，；
	2. *分区概述*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220162)，；
	3. *创建分区表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220163)，；
	4. *修改分区规则*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220169)，；
	5. *重命名分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220167)，；
	6. *添加分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220161)，；
	7. *删除分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220166)，；
	8. *Truncate 分区*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220170)，；
	9. *分区裁剪*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220168)，；
	10. *指定分区查询数据*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220164)，；




</div></div>



