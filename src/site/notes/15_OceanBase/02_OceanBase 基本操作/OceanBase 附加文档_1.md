---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/OceanBase 附加文档_1/","dgPassFrontmatter":true}
---


### 文档名称

> [!NOTE] `使用注意事项`：
> 1. 该文档 *标题* 一经使用不能修改，否则会导致其他文档链接失败，包括标题的序号；
> 2. 若新增新的标题，仅能在后面追加，否则会导致前面所有的链接失败；


#### 1 指定分区查询数据_Oracle 模式
```sql
-- 创建模板化 Range + Range 分区表 `tbl_m_rr` 并插入数据
CREATE TABLE tbl_m_rr(col1 INT,col2 INT) 
       PARTITION BY RANGE(col1)
       SUBPARTITION BY RANGE(col2)
       SUBPARTITION TEMPLATE 
        (SUBPARTITION mp0 VALUES LESS THAN(1000),
         SUBPARTITION mp1 VALUES LESS THAN(2000),
         SUBPARTITION mp2 VALUES LESS THAN(3000)
        )
        (PARTITION p0 VALUES LESS THAN(100),
         PARTITION p1 VALUES LESS THAN(200),
         PARTITION p2 VALUES LESS THAN(300)
        ); 
Query OK, 0 rows affected

INSERT INTO tbl_m_rr VALUES(10,500),(50,1100),(120,1500),(150,1800),(150,2800),(200,2900);
Query OK, 6 rows affected
Records: 6  Duplicates: 0  Warnings: 0
```

#### 2 


### 参考文档



