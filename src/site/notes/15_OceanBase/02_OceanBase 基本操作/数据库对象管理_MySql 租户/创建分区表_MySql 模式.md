---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建分区表_MySql 模式/","dgPassFrontmatter":true}
---


### 创建分区表_MySql 模式
可以根据业务需求，创建相应类型的*一级分区表* 或*二级分区表*；

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


#### 2 创建一级分区表
##### 2.1 创建 Range/Range Columns 分区的一级分区表
语法
参数解释
示例
##### 2.2 创建 List/List Columns 分区的一级分区表
语法
参数解释
示例
##### 2.3 创建 Hash/Key 分区的一级分区表
语法
参数解释
示例

#### 3 创建模板化二级分区表
##### 3.1 创建 Range Columns + Range 分区的模板化二级分区表
##### 3.2 创建 Range Columns + Range Columns 分区的模板化二级分区表
##### 3.3 创建 Range Columns + List Columns 分区的模板化二级分区表
##### 3.4 创建 Range Columns + Hash 分区的模板化二级分区表
##### 3.5 创建 Range Columns + Key 分区的模板化二级分区表
##### 3.6 创建 List + Range 分区的模板化二级分区表
##### 3.7 创建 List + Hash 分区的模板化二级分区表
##### 3.8 创建 List + Key 分区的模板化二级分区表
##### 3.9 创建 List Columns + Range Columns 分区的模板化二级分区表
##### 3.10 创建 List Columns + List Columns 分区的模板化二级分区表
##### 3.11 创建 List Columns + Hash 分区的模板化二级分区表
##### 3.12 创建 List Columns + Key 分区的模板化二级分区表
##### 3.13 创建 Hash + Range Columns 分区的模板化二级分区表
##### 3.14 创建 Hash + List Columns 分区的模板化二级分区表
##### 3.15 创建 Hash + Hash 分区的模板化二级分区表
##### 3.16 创建 Hash + Key 分区的模板化二级分区表
##### 3.17 创建 Key + Range Columns 分区的模板化二级分区表
##### 3.18 创建 Key + List Columns 分区的模板化二级分区表
##### 3.19 创建 Key + Key 分区的模板化二级分区表
#### 4 创建非模板化二级分区表
##### 4.1 创建 Range + Range 分区的非模板化二级分区表
##### 4.2 创建 Range + List Columns 分区的非模板化二级分区表
##### 4.3 创建 Range Columns + Range Columns 分区的非模板化二级分区表
##### 4.4 创建 Range Columns + List Columns 分区的非模板化二级分区表
##### 4.5 创建 Range Columns + Hash 分区的非模板化二级分区表
##### 4.6 创建 Range Columns + Key 分区的非模板化二级分区表
##### 4.7 创建 List + Range Columns 分区的非模板化二级分区表
##### 4.8 创建 List + List Columns 分区的非模板化二级分区表
##### 4.9 创建 List Columns+ Range Columns 分区的非模板化二级分区表
##### 4.10 创建 List Columns + List Columns 分区的非模板化二级分区表
##### 4.11 创建 List Columns + Hash 分区的非模板化二级分区表
##### 4.12 创建 List Columns + Key 分区的非模板化二级分区表
##### 4.13 创建 Hash + Range Columns 分区的非模板化二级分区表
##### 4.14 创建 Hash + List Columns 分区的非模板化二级分区表
##### 4.15 创建 Hash + Hash 分区的非模板化二级分区表
##### 4.16 创建 Hash + Key 分区的非模板化二级分区表

### 参考文档

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

