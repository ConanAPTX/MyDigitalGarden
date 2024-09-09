---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 修改分区规则_MySql 模式/","dgPassFrontmatter":true}
---



### 文档名称
表创建成功后，您可以根据业务需要，修改分区规则，即修改表的【*分区方式*】和【*分区类型*】；


#### 1 分区类型和分区方式转换的支持情况
在 Oceanbase 数据库 V2.x，V3.x 版本中，不支持【`非分区表`】改成【`分区表`】的功能；

从 OceanBase 数据库 MySQL 模式从 `V4.0.0 版本` 开始支持修改分区表的【*分区方式*】和【*分区类型*】；

可以将已创建的分区表的分区类型修改为任意其他类型；此外，OceanBase 数据库的 MySQL 模式还支持修改表的【*分区方式* 】，分区方式转换的支持情况如下表所示：

|分区方式|非分区|一级分区|二级分区|
|---|---|---|---|
|非分区|-|支持|支持|
|一级分区|不支持|-|支持|
|二级分区|不支持|支持|-|

#### 2 修改分区 
##### 2.1 修改一级分区表的分区类型
可以将当前分区表的分区类型修改为任意其他类型；

```sql
-- 1. 创建一级分区表 `tbl1_h`；
obclient> CREATE TABLE tbl1_h(c1 int, c2 datetime, PRIMARY KEY(c1, c2)) PARTITION BY HASH(c1) PARTITIONS 4;
Query OK, 0 rows affected

-- 2. 将一级分区表 `tbl1_h` 的分区类型修改为 Key 分区；
obclient> ALTER TABLE tbl1_h PARTITION BY KEY(c1) PARTITIONS 10;
Query OK, 0 rows affected
```


##### 2.2 修改二级分区表的分区类型
可以将当前分区表的分区类型修改为任意其他类型；

以将 Range + Range 分区的模板化二级分区表修改为 List Columns + Key 分区的模板化二级分区表为例，示例如下：
```sql
-- 1. 创建 Range Columns + Range Columns 分区的模板化二级分区表；
CREATE TABLE t_m_rcrc(col1 INT,col2 INT) 
	PARTITION BY RANGE COLUMNS(col1)
	SUBPARTITION BY RANGE COLUMNS(col2)
	SUBPARTITION TEMPLATE (
		SUBPARTITION mp0 VALUES LESS THAN(1000),
		SUBPARTITION mp1 VALUES LESS THAN(2000),
		SUBPARTITION mp2 VALUES LESS THAN(3000)
	)(
		PARTITION p0 VALUES LESS THAN(100),
		PARTITION p1 VALUES LESS THAN(200),
		PARTITION p2 VALUES LESS THAN(300)
	); 
Query OK, 0 rows affected

-- 2. 将分区表的分区类型修改为 List Columns + Key 分区；
ALTER TABLE t_m_rcrc 
	PARTITION BY LIST COLUMNS(col1)
    SUBPARTITION BY KEY(col2) SUBPARTITIONS 3(
	    PARTITION p0 VALUES IN(100),
        PARTITION p1 VALUES IN(200),
        PARTITION p2 VALUES IN(300)
    ); 
    Query OK, 0 rows affected
```



#### 3 修改分区方式
##### 3.1 非分区表或二级分区表转换为一级分区表

##### 3.2 非分区表或一级分区表转换为二级分区表


### 参考文档
1. *修改分区规则*：[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886350)，[V4.2.4](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001035475)，；


