---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建分区表_Oracle 模式/","dgPassFrontmatter":true}
---


### 创建分区表_Oracle 模式

可以根据业务需求，创建相应类型的【`一级分区表`】或【`二级分区表`】；

OceanBase 数据库中有两种表，分区表和非分区表；分区表是将一个表分解成多个更小的、更容易管理的部分；

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


#### 2 创建一级分区表
##### 2.1 创建 Range 类型的一级分区表
语法
参数解释
示例

##### 2.2 创建 List 类型的一级分区表

> [!NOTE] 说明：
> - List 分区的分区键不能是表达式；
> - 如果最后一个 List 分区指定了 `DEFAULT`，则不能新增分区；

```sql
-- 语法：
	CREATE TABLE table_name (column_name column_type[,column_name column_type])
	  PARTITION BY LIST (column_name[, column_name])
	    (PARTITION partition_name VALUES ( v01 [, v0N])
	     [,PARTITION partition_name VALUES ( vN1 [, vNN])]
	     [,PARTITION partition_name VALUES (DEFAULT)]
	    );
/*
-- 参数解释：
	table_name：指定表名；
	column_name：指定列名称；
	column_type：指定列的数据类型；
	partition_name：指定分区名称；
	DEFAULT：仅允许最后一个分区指定这个值，这个值没有具体的数值，并且比其他所有分区的上限都要大，也包含空值；
*/
```


```sql
-- 示例：创建一个 List 分区表 `tbl1_log_l`；
CREATE TABLE tbl1_log_l(log_id INT,log_value VARCHAR2(20))
PARTITION BY LIST(log_value)(
	PARTITION P01 VALUES ('A'),
	PARTITION P02 VALUES ( 'B' ),
	PARTITION P03 VALUES ( 'C' )
);
Query OK, 0 rows affected
```


##### 2.3 创建 Hash 类型的一级分区表
语法
参数解释
示例
#### 3 创建模板化二级分区表
##### 3.1 创建 Range + Range 分区的模板化二级分区表
##### 3.2 创建 Range + List 分区的模板化二级分区表
##### 3.3 创建 Range + Hash 分区的模板化二级分区表
##### 3.4 创建 List + Range 分区的模板化二级分区表
##### 3.5 创建 List + List 分区的模板化二级分区表
##### 3.6 创建 List + Hash 分区的模板化二级分区表
##### 3.7 创建 Hash + Range 分区的模板化二级分区表
##### 3.8 创建 Hash + List 分区的模板化二级分区表
##### 3.9 创建 Hash +Hash 分区的模板化二级分区表

#### 4 创建非模板化二级分区表
##### 4.1 创建 Range + Range 分区的非模板化二级分区表
##### 4.2 创建 Range + List 分区的非模板化二级分区表
##### 4.3 创建 Range + Hash 分区的非模板化二级分区表
##### 4.4 创建 List + Range 分区的非模板化二级分区表
##### 4.5 创建 List + List 分区的非模板化二级分区表
##### 4.6 创建 List +Hash 分区的非模板化二级分区表
##### 4.7 创建 Hash + Range 分区的非模板化二级分区表
##### 4.8 创建 Hash + List 分区的非模板化二级分区表
##### 4.9 创建 Hash + Hash 分区的非模板化二级分区表


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




