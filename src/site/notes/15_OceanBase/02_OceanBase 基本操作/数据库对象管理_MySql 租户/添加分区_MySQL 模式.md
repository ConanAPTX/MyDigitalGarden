---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/添加分区_MySQL 模式/","dgPassFrontmatter":true}
---



### 添加分区_MySQL 模式
创建分区表后，您可以为分区表添加分区；

#### 1 分区表对添加分区操作的支持情况
OceanBase 数据库的 MySQL 模式中，一级分区表和二级分区表对添加分区操作的具体支持情况如下表所示：

| 分区表     | 分区类型                                                                                                                                                 | 添加一级分区 | 添加二级分区 | 备注 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------ | ---- |
| 一级分区表 | Range; Range Columns; List; List Columns;                                                                                                                | 支持         | -            |      |
| 一级分区表 | Hash; Key                                                                                                                                                | 不支持       | -            |      |
| 二级分区表 | Range + Range; Range + Range Columns; Range + List; Range + List Columns; Range + Hash; Range + Key;                                                     | 支持         | 不支持       |      |
| 二级分区表 | Range Columns + Range; Range Columns + Range Columns; Range Columns + List; <br>Range Columns + List Columns; Range Columns + Hash; Range Columns + Key; | 支持         | 不支持       |      |
| 二级分区表 | List + Range; List + Range Columns; List + List; List + List Columns; List + Hash; List + Key;                                                           | 支持         | 不支持       |      |
| 二级分区表 | List Columns + Range; List Columns + Range Columns; List Columns + List; <br>List Columns + List Columns; List Columns + Hash; List Columns + Key;       | 支持         | 不支持       |      |
| 二级分区表 | Hash + Range; Hash + Range Columns; Hash + List; Hash + List Columns; Hash + Hash; Hash + Key;                                                           | 不支持       | 不支持       |      |
| 二级分区表 | Key + Range; Key + Range Columns; Key + List; Key + List Columns; Key + Hash; Key + Key;                                                                 | 不支持       | 不支持       |      |


#### 2 添加一级分区
可以向一级分区表、模板化二级分区表和非模板化二级分区表中添加一级分区；

##### 2.1 向一级分区表和模板化二级分区表中添加一级分区

##### 2.2 向非模板化二级分区表中添加一级分区
 
#### 3 添加二级分区
在 OceanBase 数据库 MySQL 模式 `V4.0.0 版本`暂不支持向表中添加二级分区；

### 参考文档
1. *添加分区*：[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886351)，；



