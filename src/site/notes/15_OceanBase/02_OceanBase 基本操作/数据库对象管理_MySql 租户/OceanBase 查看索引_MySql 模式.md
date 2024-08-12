---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 查看索引_MySql 模式/","dgPassFrontmatter":true}
---


### OceanBase 查看索引_MySql 模式


```sql
-- 查看表的索引  
SHOW INDEX FROM test; -- 查看表 test 的索引  
SHOW INDEX FROM test;
+-------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+
| Table       | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment   | Index_comment | Visible |
+-------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+
| test        |          0 | PRIMARY  |            1 | w_id        | A         |        NULL | NULL     | NULL   |      | BTREE      | available |               | YES     |
| test        |          0 | w_name   |            1 | w_name      | A         |        NULL | NULL     | NULL   | YES  | BTREE      | available |               | YES     |
| test        |          0 | w_name   |            2 | w_city      | A         |        NULL | NULL     | NULL   | YES  | BTREE      | available |               | YES     |
+-------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+
3 rows in set

/*  字段解释如下：  
	Non_unique：如果索引不能包括重复值则为0，如果可以则为1。也就是平时所说的唯一索引；  
	Key_name：索引名；  
	Seq_in_index：索引中的列序列号，一个是 1 一个是 2，用于表明该索引在联合索引中的顺序；  
	Column_name：索引的列名；  
	Collation：指的是列以什么方式存储在索引中；  
	Cardinality：是基数的意思，表示索引中唯一值的数目的估计值；  
	Sub_part：前缀索引，如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为 NULL； 
	Packed：指示关键字如何被压缩。如果没有被压缩，则为NULL；   
	Index_type：表示索引类型，当前仅支持 BTREE 类型；
	Comment：表示该索引是否可用；
    Index_comment：该索引的注释信息；
    Visible：表示该索引是否可见；
*/  
```



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/my-sql/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 查询表结构，表定义  
```sql  
-- 1.查询表的定义
DESC test;       -- 查询表结构  
DESCRIBE test;   -- 查询表结构  

-- 2.查询表的定义
SHOW CREATE TABLE test;  -- 查询表的定义
/*  
DEFAULT CHARSET = utf8mb4 指定默认字符集为 utf8mb4；  
ROW_FORMAT = DYNAMIC 表示这张表为动态表，若一张表里面存在 varchar、text 以及其变形、blob以及其变形的字段的话，那么这张表其实也叫动态表，
	即该表的 row_format 是 dynamic；该表 row_format 是 fixed 那么张这个表是静态表。  
COMPRESSION 该字段信息显示的是表压缩方式；  
PCTFREE 字段信息表示留给更新该块数据使用空间；  
*/
```  



</div></div>


### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#830d2c" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



2. `创建和管理索引` 

</div></div>

