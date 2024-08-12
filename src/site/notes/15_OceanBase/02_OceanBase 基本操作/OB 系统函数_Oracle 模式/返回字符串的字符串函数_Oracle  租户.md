---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/OB 系统函数_Oracle 模式/返回字符串的字符串函数_Oracle  租户/","dgPassFrontmatter":true}
---


### 返回字符串的字符串函数_Oracle 租户
#### 1 字母大小写转换 
##### 1.1 UPPER
该函数将字符串中英文字母全部转为大写；

```sql
/*
语法：UPPER(char)
参数解释：
	`char` 是要转换的字符串，数据类型可以为 `CHAR`、`VARCHAR2`、`NCHAR`、`NVARCHAR2` 或 `CLOB`；
返回类型：
	返回与 `char` 相同的数据类型；
*/

-- 使用示例
obclient> SELECT UPPER('OceanBase') "UPPER" FROM DUAL;   -- 将字符串 `OceanBase` 全部转换为大写
+-----------+
| UPPER     |
+-----------+
|  |
+-----------+
1 row in set
```

`官方文档`：*UPPER*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000823191)，该函数将字符串中英文字母全部转为大写；

##### 1.2 LOWER
该函数将字符串中英文字母全部转为小写；

```sql
/*
语法：LOWER(char)
参数解释：
	`char` 表示字符串。字符串类型可为 `CHAR`、`VARCHAR2`、`NCHAR`、`NVARCHAR2` 或 `CLOB`；
返回类型：
	返回与 `char` 相同的数据类型；
*/

-- 使用示例
obclient> SELECT  LOWER('AaBbCcDd') Lowercase FROM DUAL;  -- 将字符串 `AaBbCcDd` 中的英文字母全部转化为小写
+-----------+
| LOWERCASE |
+-----------+
| aabbccdd  |
+-----------+
1 row in set
```

`官方文档`：*LOWER*，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000823196)，该函数将字符串中英文字母全部转为小写；

#### 2 


### 参考文档



