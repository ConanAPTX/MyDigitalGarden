---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/修改分区规则_Oracle 模式/","dgPassFrontmatter":true}
---


### 文档名称

#### 1 修改分区规则_Oracle 模式
表创建成功后，您可以根据业务需要，修改分区规则，即可以修改表的分区方式和分区类型；

##### 1.1 分区方式和分区类型转换的支持情况

OceanBase 数据库的 Oracle 模式支持将非分区表转化为一级分区表或二级分区表，暂不支持修改分区表的分区类型。

OceanBase 数据库 Oracle 模式中分区方式转换的支持情况如下表所示。

| 分区方式  | 非分区 | 一级分区 | 二级分区 | 备注 |
| :-------------: | :----------: | :------------: |:------------ |:------------ |
| 非分区 | - | 支持 | 支持 |  |
| 一级分区 | 不支持 | - | 不支持 |  |
| 二级分区 | 不支持 | 不支持 | - |  |


##### 1.2 非分区表转化为一级分区表

##### 1.3 非分区表转化为二级分区表


#### 2 重命名分区

> [!NOTE] `注意事项`：
> 1. 分区重命名仅修改主表的分区名，不会修改主表所属的局部索引的分区名。但是，将分区重命名后，如果对该分区表执行了删除列等会重建局部索引的操作时，局部索引的分区名会变成与主表的分区名一致；
> 2. 重命名分区时，新的分区名不能与表中已有的一级分区名或二级分区名相同；
> 3. 在 OceanBase 数据库中，分区名对大小写不敏感，故不支持将分区名重命名为与原分区名大小写不同的分区名；

##### 2.1 重命名一级分区名
可以使用 `RENAME PARTITION` 子句重命名分区；

```sql
-- 重命名分区
-- 语法：ALTER TABLE table_name RENAME PARTITION partition_name TO new_partition_name;
ALTER TABLE T2_F_RR RENAME PARTITION P0 TO NEW_P0;  -- 将一级分区 `P0` 重命名为 `NEW_P0`;

-- 创建成功后，查看分区表的分区名称；
SELECT table_name,partition_name,subpartition_name FROM USER_TAB_SUBPARTITIONS WHERE table_name = 'T2_F_RR';
```

执行重命名一级分区名操作后，一级分区表可以通过视图 `USER_TAB_PARTITIONS` 来确认分区名是否修改成功；二级分区表可以通过视图 `USER_TAB_SUBPARTITIONS` 来确认分区名是否修改成功；

示例如下：

```sql
-- 1. 创建非模板化二级分区表 `t2_f_rr`
CREATE TABLE t2_f_rr(col1 INT,col2 INT)
	PARTITION BY RANGE(col1)
	SUBPARTITION BY RANGE(col2)(
		PARTITION p0 VALUES LESS THAN(100)(
			SUBPARTITION sp0 VALUES LESS THAN(2020),SUBPARTITION sp1 VALUES LESS THAN(2021)
		),
		PARTITION p1 VALUES LESS THAN(200)(
			SUBPARTITION sp2 VALUES LESS THAN(2020),SUBPARTITION sp3 VALUES LESS THAN(2021),SUBPARTITION sp4 VALUES LESS THAN(2022)
		)
	);

-- 2. 创建成功后，查看分区表的分区名称；
obclient> SELECT table_name,partition_name,subpartition_name FROM USER_TAB_SUBPARTITIONS WHERE table_name = 'T2_F_RR';
查询结果如下：
+------------+----------------+-------------------+
| TABLE_NAME | PARTITION_NAME | SUBPARTITION_NAME |
+------------+----------------+-------------------+
| T2_F_RR    | P0             | SP0               |
| T2_F_RR    | P0             | SP1               |
| T2_F_RR    | P1             | SP2               |
| T2_F_RR    | P1             | SP3               |
| T2_F_RR    | P1             | SP4               |
+------------+----------------+-------------------+
5 rows in set

-- 3.将一级分区 `P0` 重命名为 `NEW_P0`
ALTER TABLE T2_F_RR RENAME PARTITION P0 TO NEW_P0;

-- 4.再次查看分区名，可以看到分区名修改成功；
obclient> SELECT table_name,partition_name,subpartition_name FROM SYS.USER_TAB_SUBPARTITIONS WHERE table_name = 'T2_F_RR';
查询结果如下：
+------------+----------------+-------------------+
| TABLE_NAME | PARTITION_NAME | SUBPARTITION_NAME |
+------------+----------------+-------------------+
| T2_F_RR    | NEW_P0         | SP0               |
| T2_F_RR    | NEW_P0         | SP1               |
| T2_F_RR    | P1             | SP2               |
| T2_F_RR    | P1             | SP3               |
| T2_F_RR    | P1             | SP4               |
+------------+----------------+-------------------+
5 rows in set
```


##### 2.2 重命名二级分区名

重命名二级分区名的语句如下：
```sql
-- 重命名分区
-- 语法：ALTER TABLE table_name RENAME SUBPARTITION partition_name TO new_partition_name;
ALTER TABLE T3_F_RR RENAME SUBPARTITION SP1 TO NEW_SP1;  -- 将二级分区 `SP1` 重命名为 `NEW_SP1`。

-- 创建成功后，查看分区表的分区名称；
SELECT table_name,partition_name,subpartition_name FROM USER_TAB_SUBPARTITIONS WHERE table_name = 'T3_F_RR';
```

执行重命名二级分区名操作后，可以通过查询视图 `USER_TAB_SUBPARTITIONS` 来确认分区名是否修改成功；

示例如下：
```sql
-- 1.创建非模板化二级分区表 `t3_f_rr`
CREATE TABLE t3_f_rr(col1 INT,col2 INT)
	PARTITION BY RANGE(col1)
	SUBPARTITION BY RANGE(col2)(
		PARTITION p0 VALUES LESS THAN(100)(
			SUBPARTITION sp0 VALUES LESS THAN(2020),SUBPARTITION sp1 VALUES LESS THAN(2021)
		),
		PARTITION p1 VALUES LESS THAN(200)(
			SUBPARTITION sp2 VALUES LESS THAN(2020),SUBPARTITION sp3 VALUES LESS THAN(2021),SUBPARTITION sp4 VALUES LESS THAN(2022)
		)
	);

-- 2. 创建成功后，查看分区表的分区名称；
obclient> SELECT table_name,partition_name,subpartition_name FROM USER_TAB_SUBPARTITIONS WHERE table_name = 'T3_F_RR';
查询结果如下：
    +------------+----------------+-------------------+
    | TABLE_NAME | PARTITION_NAME | SUBPARTITION_NAME |
    +------------+----------------+-------------------+
    | T3_F_RR    | P0             | SP0               |
    | T3_F_RR    | P0             | SP1               |
    | T3_F_RR    | P1             | SP2               |
    | T3_F_RR    | P1             | SP3               |
    | T3_F_RR    | P1             | SP4               |
    +------------+----------------+-------------------+
    5 rows in set

-- 3.将二级分区 `SP1` 重命名为 `NEW_SP1`。
ALTER TABLE T3_F_RR RENAME SUBPARTITION SP1 TO NEW_SP1;

-- 4. 再次查看分区名，可以看到分区名修改成功
obclient> SELECT table_name,partition_name,subpartition_name FROM SYS.USER_TAB_SUBPARTITIONS WHERE table_name = 'T3_F_RR';
查询结果如下：
    +------------+----------------+-------------------+
    | TABLE_NAME | PARTITION_NAME | SUBPARTITION_NAME |
    +------------+----------------+-------------------+
    | T3_F_RR    | P0             | SP0               |
    | T3_F_RR    | P0             | NEW_SP1           |
    | T3_F_RR    | P1             | SP2               |
    | T3_F_RR    | P1             | SP3               |
    | T3_F_RR    | P1             | SP4               |
    +------------+----------------+-------------------+
    5 rows in set
```


### 2.2 参考文档



