---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/OB 系统函数_MySql 模式/REGEXP_LIKE，MySql 模式/","dgPassFrontmatter":true}
---


### REGEXP_LIKE，MySql 模式
#### 1 适用版本


#### 2 声明及说明

```sql
-- 语法：
	REGEXP_LIKE(expr, pat[, match_type])

-- 说明：
	如果字符串 `expr` 匹配到正则表达式 `pat` 中指定的值，则返回 `1`，否则返回 `0`；如果 `expr` 或 `pat` 为 `NULL`，则返回 `NULL`；
	match_type 具有以下取值，表示匹配模式：
		c 表示区分大小写；
	    i 表示不区分大小写；
	    m 表示识别字符串中的换行符。默认情况下，只匹配字符串表达式开始和结束处的换行符；
	    n 表示句号 `.` 仅字符匹配换行符（`\n`）。默认情况下，`.` 匹配任何非换行符（`\n`）的单字符；
	    u 表示仅匹配 Unix 行结尾。只有换行符（`\n`）可以被 `.`、`^` 与 `$` 识别为行结尾；
```


#### 3 使用示例
```sql
SELECT REGEXP_LIKE('OceanBase', 'OCEANBASE');
+---------------------------------------+
| REGEXP_LIKE('OceanBase', 'OCEANBASE') |
+---------------------------------------+
|                                     1 |
+---------------------------------------+
1 row in set

SELECT REGEXP_LIKE('OceanBase', 'OCEANBASE','c');
+-------------------------------------------+
| REGEXP_LIKE('OceanBase', 'OCEANBASE','c') |
+-------------------------------------------+
|                                         0 |
+-------------------------------------------+
1 row in set

SELECT REGEXP_LIKE('Ocean\nBase', 'OCEAN.BASE');
+------------------------------------------+
| REGEXP_LIKE('Ocean\nBase', 'OCEAN.BASE') |
+------------------------------------------+
|                                        0 |
+------------------------------------------+
1 row in set

SELECT REGEXP_LIKE('Ocean\nBase', 'OCEAN.BASE','n');
+----------------------------------------------+
| REGEXP_LIKE('Ocean\nBase', 'OCEAN.BASE','n') |
+----------------------------------------------+
|                                            1 |
+----------------------------------------------+
1 row in set
```


### 参考文档
1. *REGEXP_LIKE*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000823466)，；


