---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/OceanBase 模式匹配条件_Oracle 模式/","dgPassFrontmatter":true}
---


### OceanBase 模式匹配条件_Oracle 模式
模式匹配条件类型包括 `LIKE` 条件和 `REGEXP_LIKE` 条件；

#### 1 LIKE 条件
`LIKE` 条件用于模式匹配。相等运算符（=）指的是一个字符值与另一个字符值完全匹配，而 `LIKE` 条件通过在第一个值中搜索由第二个字符值指定的模式来将一个字符值的一部分与另一个字符值进行匹配；

`LIKE` 使用输入字符集定义的字符来计算字符串；

##### 1.1 LIKE 条件语法：

> [!NOTE] 使用说明：
> 1. 当 `LIKE` 条件中包含"||"字符时，可以将其作为逻辑或运算符，由 `sql_mode` 进行控制：
> 	1. 当 `sql_mode` 不包含 `PIPES_AS_CONCAT` 时，`||` 被识别为逻辑或运算符；
> 	2. 当 `sql_mode` 包含 `PIPES_AS_CONCAT` 时，`||` 被识别为字符连接运算符；

```sql
/* LIKE 条件语法：
	char1 [NOT] LIKE char2 [ ESCAPE esc_char ]
参数解释：
	char1：字符表达式，例如字符列，称为搜索值；
	char2：字符表达式，通常是字面量，称为模式；
	esc_char：字符表达式，通常是字面量，ESCAPE 将 esc_char 标识为转义字符。当转义符置于模式匹配符之前时，该模式匹配符被解释为普通的字符；
*/
```


> [!NOTE] `使用说明`：
> 1. 如果未指定 `esc_char`，则没有默认转义字符。如果 `char1`、`char2` 或 `esc_char` 中的任何一个为 NULL，则结果未知。否则，转义字符（如果指定）必须是长度为 1 的字符串；
> 2. 所有字符表达式（`char1`、`char2` 和 `esc_char`）都可以是任何数据类型，例如 `CHAR`、`VARCHAR2`、`NCHAR` 或 `NVARCHAR2`。
> 	1. 如果它们的数据类型不同，则 OceanBase 数据库将它们全部转换为 `char1` 的数据类型；
> 3. 模式可以包含如下特殊的模式匹配字符：
> 	1. 下划线 (\_) 表示正好匹配值中的一个字符（而不是多字节字符集中的一个字节）；
> 	2. 百分号 (%) 表示可以匹配值中的零个或多个字符（与多字节字符集中的字节相反）。模式"%"不能匹配 NULL；
> 	3. `LIKE` 检测到无通配符时可以转换成 `=`，例如 `SELECT * FROM t1 WHERE c1 LIKE 'ABC';` 等价于 “filter("c1"='abc')”


##### 1.2 使用示例：
```sql
-- 使用示例
-- 示例1：
SELECT last_name FROM emp WHERE last_name LIKE '%A\_B%' ESCAPE '\' ORDER BY last_name;
SELECT last_name FROM emp WHERE last_name LIKE '%A\_B%' ESCAPE '\' ORDER BY last_name;
	-- `ESCAPE '\'` 会将 `%A\_B%` 中 `\` 后面的模式匹配符'_'解释为普通的字符；

-- 示例2：
	-- 创建表并插入测试数据
	DROP TABLE IF EXISTS tab_99;
	CREATE TABLE IF NOT EXISTS tab_99(col1 INT,col2 VARCHAR(50),PRIMARY KEY (col1));
	INSERT INTO tab_99 VALUES(1,'test1');
	INSERT INTO tab_99 VALUES(2,'te碶st2');

	-- 查询示例
	SELECT @@character_set_database, @@collation_database;  -- 查询当前数据库的字符集，字符序
	SELECT * FROM tab_99 WHERE MC LIKE '%碶%';              -- 字符集为 utf8mb4 执行匹配有结果；字符集为 GBK 则无法匹配结果(尚未验证);
	SELECT * FROM tab_99 WHERE MC LIKE '%碶%' ESCAPE '\';

```

*OceanBase V3.1.2 之前版本使用模糊查询 LIKE*：[[15_OceanBase/80_OB 相关知识库/OMS 错误码#1.1 CONNECTOR-SOOO0400601\|OMS 错误码#1.1 CONNECTOR-SOOO0400601]]，；

#### 2 REGEXP_LIKE 条件
`REGEXP_LIKE` 用于正则表达式匹配。`REGEXP_LIKE` 使用输入字符集定义的字符评估字符串；

##### 2.1 REGEXP_LIKE 条件语法

```sql
/*
REGEXP_LIKE 条件语法：
	REGEXP_LIKE(source_char, pattern [, match_param ])
参数解释：
	source_char：用作搜索值的字符表达式，数据类型可以是 CHAR、VARCHAR2、NCHAR、NVARCHAR2 或 CLOB。
	pattern：正则表达式，通常是文本字面量，数据类型可以是 CHAR、VARCHAR2、NCHAR 或 NVARCHAR2。
	match_param：是一个数据类型 VARCHAR2 或 CHAR 的字符表达式，用于更改条件的默认匹配行为；
		您可以为 match_parameter 指定以下一个或多个值：
			'i' 指定不区分大小写的匹配。
			'c' 指定区分大小写的匹配；
			'n' 允许句点 (.) 来匹配换行符。如果省略此参数，则句点与换行符不匹配。
			'm' 表示将源字符串视为多行。OceanBase 数据库将 `^` 和 `$` 分别解释为源字符串中任意行的开头和结尾，而不是仅局限在整个源字符串的开头或结尾处。
				如果省略此参数，则 OceanBase 数据库将源字符串视为单行。
			'x' 表示忽略空白字符。默认情况下，空白字符与自身匹配；
*/
```

> [!NOTE] `使用说明`：
> 1. `pattern` 最多可以包含 512 个字节。如果 `pattern` 的数据类型与 `source_char` 的数据类型不同，则OceanBase 数据库将 `pattern` 转换为 `source_char` 的数据类型。
> 2. 如果您为 `match_parameter` 指定多个相互矛盾的值，则 OceanBase 数据库使用最后一个值。例如，如果您指定 'ic'，则 OceanBase 数据库使用区分大小写的匹配。如果您指定的字符不属于上述值，则将返回错误；
> 3. 如果省略 `match_parameter`，则遵从以下匹配规则：
> 	1. 默认区分大小写由 `NLS_SORT` 系统变量的值决定；
> 	2. 句点 (.) 与换行符不匹配；
> 	3. 源字符串被视为单行；


##### 2.2 使用示例：
```sql
-- 创建表 `emp`，并向里面插入数据
CREATE TABLE emp(manager_id INT, first_name varchar(50), last_name varchar(50), hiredate varchar(50),SALARY INT);
INSERT INTO emp VALUES(300, 'Steven', 'King',  '2019-09-11',23600);     
INSERT INTO emp VALUES(200, 'Steven', 'Markle', '2019-11-05', 23800);
INSERT INTO emp VALUES(100, 'Deven',  'Part',  '2018-10-01',24000);     
INSERT INTO emp VALUES(200, 'Carlos', 'Ross',  '2019-06-11',23500);     
INSERT INTO emp VALUES(200, 'Teven',  'Bell', '2019-05-25', 23000);
INSERT INTO emp VALUES(200, 'Stephen', 'Stiles',  '2018-06-11',24500);    
INSERT INTO emp VALUES(100, 'Ame', 'De Haan', '2018-05-01',11000);      
INSERT INTO emp VALUES(100, 'Jon', 'Errazuriz', '2017-07-21', 1400);   
COMMIT;

-- 查询返回名字为 `Steven` 或 `Stephen` 的员工的名字和姓氏；
SELECT first_name, last_name FROM emp WHERE REGEXP_LIKE (first_name, '^Ste(v|ph)en$') ORDER BY first_name, last_name;
	-- 其中 first_name 以 Ste 开头，以 en 结尾，中间是 v 或 ph；
+------------+-----------+
| FIRST_NAME | LAST_NAME |
+------------+-----------+
| Stephen    | Stiles    |
| Steven     | King      |
| Steven     | Markle    |
+------------+-----------+
3 rows in set

-- 查询返回姓氏中带有双元音的员工的姓氏；
SELECT last_name FROM emp WHERE REGEXP_LIKE (last_name, '([aeiou])\1', 'i') ORDER BY last_name;
	-- 其中 last_name 包含两个相邻的 a、e、i、o 或 u，不区分大小写；
+-----------+
| LAST_NAME |
+-----------+
| De Haan   |
+-----------+
1 row in set
```



### 参考文档
1. *模式匹配条件*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221340)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821946)，；


