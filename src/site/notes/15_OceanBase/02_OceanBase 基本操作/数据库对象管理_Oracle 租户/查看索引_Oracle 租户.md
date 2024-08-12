---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/查看索引_Oracle 租户/","dgPassFrontmatter":true}
---


### 查看索引_Oracle 租户

#### 1 查看索引信息
可以通过 `USER_INDEXES`，`ALL_INDEXES`，`DBA_INDEXES` 系统视图查看索引，视图详细情况：[[15_OceanBase/99_内部表介绍/Oracle 租户索引信息_01#1 查看索引信息\|Oracle 租户索引信息_01#1 查看索引信息]]，；

##### 1.1 查看用户拥有的所有表的索引信息
```sql
-- USER_INDEXES：用于查看用户拥有的所有表的索引信息；
SELECT * FROM USER_INDEXES WHERE table_name='TEST';  
SELECT * FROM USER_INDEXES WHERE table_name='TEST';
+-------------+------------+-------------+------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
| INDEX_NAME  | INDEX_TYPE | TABLE_OWNER | TABLE_NAME | TABLE_TYPE | UNIQUENESS | COMPRESSION | PREFIX_LENGTH | TABLESPACE_NAME | INI_TRANS | MAX_TRANS | INITIAL_EXTENT | NEXT_EXTENT | MIN_EXTENTS | MAX_EXTENTS | PCT_INCREASE | PCT_THRESHOLD | INCLUDE_COLUMN | FREELISTS | FREELIST_GROUPS | PCT_FREE | LOGGING | BLEVEL | LEAF_BLOCKS | DISTINCT_KEYS | AVG_LEAF_BLOCKS_PER_KEY | AVG_DATA_BLOCKS_PER_KEY | CLUSTERING_FACTOR | STATUS | NUM_ROWS | SAMPLE_SIZE | LAST_ANALYZED | DEGREE | INSTANCES | PARTITIONED | TEMPORARY | GENERATED | SECONDARY | BUFFER_POOL | FLASH_CACHE | CELL_FLASH_CACHE | USER_STATS | DURATION | PCT_DIRECT_ACCESS | ITYP_OWNER | ITYP_NAME | PARAMETERS | GLOBAL_STATS | DOMIDX_STATUS | DOMIDX_OPSTATUS | FUNCIDX_STATUS | JOIN_INDEX | IOT_REDUNDANT_PKEY_ELIM | DROPPED | VISIBILITY | DOMIDX_MANAGEMENT | SEGMENT_CREATED |
+-------------+------------+-------------+------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
| T5_NAME_IND | NORMAL     | SYS         | TEST       | TABLE      | NONUNIQUE  | ENABLED     |          NULL | NULL            |      NULL |      NULL |           NULL |        NULL |        NULL |        NULL |         NULL |          NULL |           NULL |      NULL |            NULL |     NULL | NULL    |   NULL |        NULL |          NULL |                    NULL |                    NULL |              NULL | VALID  |     NULL |        NULL | NULL          | 1      | NULL      | NO          | NULL      | NULL      | NULL      | NULL        | NULL        | NULL             | NULL       | NULL     |              NULL | NULL       | NULL      | NULL       | NULL         | NULL          | NULL            | NULL           | NO         | NULL                    | NO      | VISIBLE    | NULL              | NULL            |
+-------------+------------+-------------+------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
1 row in set (0.00 sec)
/*
TABLE_NAME：指定需要查询的表名为 TEST；
INDEX_TYPE：表示索引类型； 【NORMAL】：代表的是普通索引；【FUNCTION-BASED NORMAL】：函数索引；
UNIQUENESS：表示索引是否唯一，该字段显示 NONUNIQUE 表示不唯一；
*/
```



##### 1.2 查看表的所有索引
```sql
-- ALL_INDEXES: 用于查看用户可访问的所有表的索引的索引列信息；查看表的所有索引
SELECT * FROM ALL_INDEXES WHERE table_name='TEST';  
SELECT OWNER,INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,TABLE_TYPE,UNIQUENESS,COMPRESSION,STATUS,PARTITIONED,VISIBILITY 
FROM ALL_INDEXES WHERE table_name='TEST';
SELECT * FROM ALL_INDEXES WHERE table_name='TEST';
+-------+-------------+------------+-------------+------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
| OWNER | INDEX_NAME  | INDEX_TYPE | TABLE_OWNER | TABLE_NAME | TABLE_TYPE | UNIQUENESS | COMPRESSION | PREFIX_LENGTH | TABLESPACE_NAME | INI_TRANS | MAX_TRANS | INITIAL_EXTENT | NEXT_EXTENT | MIN_EXTENTS | MAX_EXTENTS | PCT_INCREASE | PCT_THRESHOLD | INCLUDE_COLUMN | FREELISTS | FREELIST_GROUPS | PCT_FREE | LOGGING | BLEVEL | LEAF_BLOCKS | DISTINCT_KEYS | AVG_LEAF_BLOCKS_PER_KEY | AVG_DATA_BLOCKS_PER_KEY | CLUSTERING_FACTOR | STATUS | NUM_ROWS | SAMPLE_SIZE | LAST_ANALYZED | DEGREE | INSTANCES | PARTITIONED | TEMPORARY | GENERATED | SECONDARY | BUFFER_POOL | FLASH_CACHE | CELL_FLASH_CACHE | USER_STATS | DURATION | PCT_DIRECT_ACCESS | ITYP_OWNER | ITYP_NAME | PARAMETERS | GLOBAL_STATS | DOMIDX_STATUS | DOMIDX_OPSTATUS | FUNCIDX_STATUS | JOIN_INDEX | IOT_REDUNDANT_PKEY_ELIM | DROPPED | VISIBILITY | DOMIDX_MANAGEMENT | SEGMENT_CREATED |
+-------+-------------+------------+-------------+------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
| SYS   | T5_NAME_IND | NORMAL     | SYS         | TEST       | TABLE      | NONUNIQUE  | ENABLED     |          NULL | NULL            |      NULL |      NULL |           NULL |        NULL |        NULL |        NULL |         NULL |          NULL |           NULL |      NULL |            NULL |     NULL | NULL    |   NULL |        NULL |          NULL |                    NULL |                    NULL |              NULL | VALID  |     NULL |        NULL | NULL          | 1      | NULL      | NO          | NULL      | NULL      | NULL      | NULL        | NULL        | NULL             | NULL       | NULL     |              NULL | NULL       | NULL      | NULL       | NULL         | NULL          | NULL            | NULL           | NO         | NULL                    | NO      | VISIBLE    | NULL              | NULL            |
+-------+-------------+------------+-------------+------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
1 row in set (0.00 sec)
/*
INDEX_TYPE：表示索引类型； 【NORMAL】：代表的是普通索引；【FUNCTION-BASED NORMAL】：函数索引；
COMPRESSION：字段信息代表是否开启索引压缩功能 默认是 `ENABLED` 状态。
PREFIX_LENGTH：表示索引压缩中被压缩的字段数量。
`INCLUDE_COLUMN` 字段包含在索引组织表主键（非溢出）索引中的最后一列的列 ID。
`DISTINCT_KEYS` 代表不同索引值的数量。
`DROPPED` 指示索引是否已被删除并且是否在回收站中；
*/
```


##### 1.3 查看数据库所有表的索引信息
```sql
-- DBA_INDEXES：用于查看数据库所有表的索引信息；
SELECT * FROM DBA_INDEXES WHERE TABLE_OWNER = UPPER('SYS') AND TABLE_NAME = UPPER('TEST');
SELECT OWNER,INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,TABLE_TYPE,UNIQUENESS,COMPRESSION,STATUS,PARTITIONED,VISIBILITY 
FROM DBA_INDEXES 
WHERE TABLE_OWNER = UPPER('SYS') AND TABLE_NAME IN (UPPER('ajjb'), UPPER('zd_ay'));
ORDER BY TABLE_OWNER,TABLE_NAME,INDEX_NAME;
	-- TABLE_OWNER, TABLE_NAME 字段的值均为大写；
+-------+---------------------------------------+------------+-------------+-------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
| OWNER | INDEX_NAME                            | INDEX_TYPE | TABLE_OWNER | TABLE_NAME  | TABLE_TYPE | UNIQUENESS | COMPRESSION | PREFIX_LENGTH | TABLESPACE_NAME | INI_TRANS | MAX_TRANS | INITIAL_EXTENT | NEXT_EXTENT | MIN_EXTENTS | MAX_EXTENTS | PCT_INCREASE | PCT_THRESHOLD | INCLUDE_COLUMN | FREELISTS | FREELIST_GROUPS | PCT_FREE | LOGGING | BLEVEL | LEAF_BLOCKS | DISTINCT_KEYS | AVG_LEAF_BLOCKS_PER_KEY | AVG_DATA_BLOCKS_PER_KEY | CLUSTERING_FACTOR | STATUS | NUM_ROWS | SAMPLE_SIZE | LAST_ANALYZED | DEGREE | INSTANCES | PARTITIONED | TEMPORARY | GENERATED | SECONDARY | BUFFER_POOL | FLASH_CACHE | CELL_FLASH_CACHE | USER_STATS | DURATION | PCT_DIRECT_ACCESS | ITYP_OWNER | ITYP_NAME | PARAMETERS | GLOBAL_STATS | DOMIDX_STATUS | DOMIDX_OPSTATUS | FUNCIDX_STATUS | JOIN_INDEX | IOT_REDUNDANT_PKEY_ELIM | DROPPED | VISIBILITY | DOMIDX_MANAGEMENT | SEGMENT_CREATED |
+-------+---------------------------------------+------------+-------------+-------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
| SYS   | TABLE_NAME1_OBPK_1639997002986248     | NORMAL     | SYS         | TABLE_NAME1 | TABLE      | UNIQUE     | ENABLED     |          NULL | NULL            |      NULL |      NULL |           NULL |        NULL |        NULL |        NULL |         NULL |          NULL |           NULL |      NULL |            NULL |     NULL | NULL    |   NULL |        NULL |          NULL |                    NULL |                    NULL |              NULL | VALID  |     NULL |        NULL | NULL          | 1      | NULL      | NO          | NULL      | NULL      | NULL      | NULL        | NULL        | NULL             | NULL       | NULL     |              NULL | NULL       | NULL      | NULL       | NULL         | NULL          | NULL            | NULL           | NO         | NULL                    | NO      | VISIBLE    | NULL              | NULL            |
+-------+---------------------------------------+------------+-------------+-------------+------------+------------+-------------+---------------+-----------------+-----------+-----------+----------------+-------------+-------------+-------------+--------------+---------------+----------------+-----------+-----------------+----------+---------+--------+-------------+---------------+-------------------------+-------------------------+-------------------+--------+----------+-------------+---------------+--------+-----------+-------------+-----------+-----------+-----------+-------------+-------------+------------------+------------+----------+-------------------+------------+-----------+------------+--------------+---------------+-----------------+----------------+------------+-------------------------+---------+------------+-------------------+-----------------+
5 rows in set (0.01 sec)
/*
`OWNER` 该字段代表当前数据库的用户为 SYS；
INDEX_TYPE：表示索引类型； 【NORMAL】：代表的是普通索引；【FUNCTION-BASED NORMAL】：函数索引；
`UNIQUENESS` 表示唯一不允许重复的索引。
PARTITIONED：VARCHAR2(3)，NO，表示索引是否是分区索引；
*/
```


#### 2 查询索引列信息
可以通过 `ALL_IND_COLUMNS`，`USER_IND_COLUMNS`，`DBA_IND_COLUMNS` 系统视图查看索引，视图详细情况：[[15_OceanBase/99_内部表介绍/Oracle 租户索引信息_01#2 查询索引列信息\|Oracle 租户索引信息_01#2 查询索引列信息]]，；

##### 2.1 查看表的索引的详细信息
查看表的索引的详细信息；
```sql
-- USER_IND_COLUMNS：查看用户拥有的所有表的索引的索引列信息；查看表的索引的详细信息  
SELECT * FROM USER_IND_COLUMNS WHERE table_name='TEST' ORDER BY INDEX_NAME, COLUMN_POSITION; 
SELECT * FROM USER_IND_COLUMNS WHERE table_name='TEST';
+-------------+------------+-------------+-----------------+---------------+-------------+---------+
| INDEX_NAME  | TABLE_NAME | COLUMN_NAME | COLUMN_POSITION | COLUMN_LENGTH | CHAR_LENGTH | DESCEND |
+-------------+------------+-------------+-----------------+---------------+-------------+---------+
| T5_NAME_IND | TEST       | NAME        |               1 |             1 |           1 | ASC     |
+-------------+------------+-------------+-----------------+---------------+-------------+---------+
1 row in set (0.02 sec)

/*
`COLUMN_POSITION` 代表列位置点信息。
`COLUMN_LENGTH` 表示列长。
`CHAR_LENGTH` 表示字符长度。
*/
```


##### 2.2 查看用户可访问的所有表的索引的索引列信息
```sql
-- ALL_IND_COLUMNS：查看用户可访问的所有表的索引的索引列信息；  
SELECT * FROM ALL_IND_COLUMNS WHERE table_name='AK_YJJEB' ORDER BY INDEX_NAME, COLUMN_POSITION;  
bclient> SELECT * FROM ALL_IND_COLUMNS ;
+-------------+---------------------------------------+-------------+-------------+-------------+-----------------+---------------+-------------+---------+
| INDEX_OWNER | INDEX_NAME                            | TABLE_OWNER | TABLE_NAME  | COLUMN_NAME | COLUMN_POSITION | COLUMN_LENGTH | CHAR_LENGTH | DESCEND |
+-------------+---------------------------------------+-------------+-------------+-------------+-----------------+---------------+-------------+---------+
| SYS         | TABLE_NAME1_OBPK_1639997002986248     | SYS         | TABLE_NAME1 | W_ID        |               1 |            22 |           0 | ASC     |
+-------------+---------------------------------------+-------------+-------------+-------------+-----------------+---------------+-------------+---------+
12 rows in set (0.02 sec)
/*
`DESCEND` 该字段显示 `ASC` 表示为升序索引。
`COLUMN_NAME` 代表列名。
*/
```


##### 2.3 查看数据库所有表的索引的索引列信息
查看数据库所有表的索引的索引列信息；
```sql
-- DBA_IND_COLUMNS：查看数据库所有表的索引的索引列信息；
SELECT * FROM DBA_IND_COLUMNS WHERE table_name='SYS' AND table_name='AK_YJJEB' ORDER BY INDEX_NAME, COLUMN_POSITION;
SELECT * FROM DBA_IND_COLUMNS 
WHERE TABLE_OWNER = UPPER('ZXNEW') AND TABLE_NAME IN (UPPER('AJJB'),UPPER('ZD_AY')) 
ORDER BY TABLE_OWNER,TABLE_NAME,INDEX_NAME, COLUMN_POSITION;
	-- TABLE_OWNER, TABLE_NAME 字段的值均为大写；
+-------------+---------------------------------------+-------------+-------------+-------------+-----------------+---------------+-------------+---------+
| INDEX_OWNER | INDEX_NAME                            | TABLE_OWNER | TABLE_NAME  | COLUMN_NAME | COLUMN_POSITION | COLUMN_LENGTH | CHAR_LENGTH | DESCEND |
+-------------+---------------------------------------+-------------+-------------+-------------+-----------------+---------------+-------------+---------+
| SYS         | TABLE_NAME1_OBPK_1639997002986248     | SYS         | TABLE_NAME1 | W_ID        |               1 |            22 |           0 | ASC     |
+-------------+---------------------------------------+-------------+-------------+-------------+-----------------+---------------+-------------+---------+
12 rows in set (0.02 sec)
/*
`INDEX_OWNER` 表示索引拥有者。
`INDEX_NAME` 索引名。
*/
```



### 参考文档
1. [创建索引](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000640018)，[创建索引](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641847)，；
2. *查看索引*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220160)，；



