---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/OceanBase 字符集与字符序/","dgPassFrontmatter":true}
---


#### 1 字符集与字符序概述  
字符集是一组符号和编码；OceanBase 数据库默认的字符集是 **utf8mb4**；为支持无缝迁移，OceanBase 数据库在语法上将 UTF8 视为 UTF8MB4 的同义词；`数据库字符集暂不支持修改；`
字符序是一组用于比较字符集中字符的规则；OceanBase 数据库默认的字符序是 **utf8mb4_general_ci**；对于任何 Unicode 字符集，使用 xxx_general_ci 排序规则执行的操作比使用 xxx_unicode_ci 排序规则执行的操作快；`字符序暂不支持修改；`  

> [!NOTE] 符序具有以下一般特征：  
> 1. 两个不同的字符集不能有相同的字符序；  
> 2. 每个字符集都有一个默认的字符序；  
> 3. 字符序名称以与它们相关联的字符集的名称开头，通常后跟一个或多个表示其他字符序特征的后缀；


##### 1.1 查询可用的字符集，字符序  
```sql  
-- 查询可用的字符集(查询系统支持的字符集)  
SHOW CHARSET;       -- Oracle 租户  
SHOW CHARACTER SET; -- MySql 租户  
  
-- 查询所有可用的字符序，即字符集排序规则  
SHOW COLLATION; -- Oracle 租户，MySql 租户  
	-- Default 列：表示该字符序是否是其字符集的默认值，若值为 Yes，则表示其是默认值，否则为空；  
```


##### 1.2 客户端连接的字符集，字符序  
`连接`是指客户端程序连接到服务器时建立的会话，以开始与服务器的交互；
客户端通过会话连接发送 SQL 语句；服务器通过连接将响应（例如结果集或错误消息）发送回客户端；  

###### 1.2.1 查询当前会话的字符集与排序规则  
```sql  
-- 查询变量的值  
SHOW SESSION VARIABLES LIKE 'character_set_c%';
	SHOW VARIABLES LIKE 'character_set_client';       -- character_set_client：系统变量用于设置客户端发送语句的字符集；
	SHOW VARIABLES LIKE 'character_set_connection';   -- character_set_connection：系统变量用于设置收到语句后应转换的字符集； 
SHOW VARIABLES LIKE 'collation_connection';           -- collation_connection：系统变量用于设置连接使用的字符集和字符序；
SHOW SESSION VARIABLES LIKE 'collation_%';  
SHOW VARIABLES LIKE 'character_set_filesystem';
SHOW VARIABLES LIKE 'character_set_results'; 
SHOW VARIABLES LIKE 'character_set_system';
/*  
相关变量详细介绍：
	character_set_server，collation_server：系统变量指定服务器的字符集和字符序；  
	character_set_database，collation_database：系统变量指定数据库的默认字符集和字符序；  
	character_set_client：系统变量用于设置客户端发送语句的字符集；  
	character_set_connection：系统变量用于设置收到语句后应转换的字符集；  
		服务器将客户端发送的语句从 character_set_client 转换为 character_set_connection；  
	collation_connection：系统变量用于设置连接使用的字符集和字符序。collation_connection 对于字符串的比较很重要；  
		用于设置连接使用的字符集和字符序；根据需要设置，一般保持默认值；  
	character_set_results：系统变量用于设置服务器向客户端返回查询结果的字符集，包括数据结果（例如列值）、元数据（例如列名称）和错误消息；  
*/  
```  


###### 1.2.2 配置客户端字符集的 SQL 语句  
设置当前会话的字符集，字符序（字符集排序规则）；
建立连接后，客户端可以更改当前会话的字符集和字符序的系统变量。可以使用如下 SET 语句更改这些变量：

```sql  
-- 设置当前会话的字符集与排序规则  
/*   
作用：用来设定客户端会在之后的请求中使用的字符集；  
语法：  
	SET NAMES 'charset_name';  
	SET NAMES 'charset_name' [COLLATE 'collation_name'];  
		-- 要指定用于 collation_connection 的字符序，请添加 COLLATE 子句；  
参数解释：  
	charset_name：指定字符集；   
	collation_name：指定字符集的排序规则；如未指定，则使用字符集默认字符集排序规则；  
*/  
--   
SET NAMES 'gbk';                     -- 将当前会话的字符集设置为 `gbk`，默认字符集排序规则
SET NAMES 'utf8mb4';                 -- 设置字符集，排序规则使用该字符集的默认排序规则  
	-- 该语句等效于以下三个语句：  
	SET character_set_client = 'utf8mb4';  
	SET character_set_results = 'utf8mb4';  
	SET character_set_connection = 'utf8mb4';  
SET NAMES 'utf8mb4' COLLATE 'utf8mb4_general_ci';  -- 将当前会话的字符集设置为 utf8mb4，排序规则为 utf8mb4_general_ci
	-- 要指定用于 collation_connection 的字符序，请添加 COLLATE 子句；  
```  

```sql  
-- 用来设定客户端会在之后的请求中使用的字符集；  
	-- 语法：SET CHARACTER SET 'charset_name'  
	SET CHARACTER SET 'utf8mb4'  
		-- 该语句等效于以下三个语句：  
		SET character_set_client = charset_name;  
		SET character_set_results = charset_name;  
		SET collation_connection = @@collation_database;  
```  


#### 2 字符集与字符序详解  
字符集与字符序可以在以下层级设置：数据库级，表级，列级，字符表达式集，客户端连接；  

##### 2.1 数据库级字符集和字符序  
每个数据库都有*默认字符集* 和 *默认字符序*，其可以通过 character_set_database 和 collation_database 系统变量的值来确定，也可以通过询 `INFORMATION_SCHEMA.SCHEMATA` 表来实现；详情：[MySql_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357413)，；  
```sql  
-- 查询数据库的的默认字符集和字符序  
USE database_name;  
SELECT @@character_set_database, @@collation_database;   -- 查询当前数据库的字符集，字符序
  
-- 查询数据库的的默认字符集和字符序  
SELECT SCHEMA_NAME, DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = 'database_name';  
```  


##### 2.2 表级字符集和字符序  
每张表都有默认字符集和默认字符序；详情：[表级字符集和字符序_MySql_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000000708295)，；  
```sql  
-- 创建表时指定表的字符集和字符序，语法如下：  
CREATE TABLE table_name (column_list)  
    [[DEFAULT] CHARACTER SET charset_name]  
    [COLLATE collation_name]]  
  
-- 创建表并指定了表的字符集和字符序  
CREATE TABLE t (col1 VARCHAR(5))DEFAULT CHARSET = utf8mb4 COLLATE utf8mb4_general_ci;  
  
-- 查看表的定义  
obclient> SHOW CREATE TABLE test;  
	-- DEFAULT CHARSET = utf8mb4：指定表的默认字符集；  
	-- COLLATE utf8mb4_general_ci：指定表的默认字符序；  
```  


###### 2.2.1 选择表的字符集和字符序  

> [!NOTE] OceanBase 数据库通过以下方式选择表的字符集和字符序：  
> 1. 若同时指定了字符集和字符序，则使用字符集 charset_name 和字符序 collation_name；
> 2. 若指定了字符集但未指定 COLLATE，则使用字符集 charset_name 及其默认字符序； 
> 	1. 要查看每个字符集的默认字符序，请使用 SHOW CHARACTER SET 语句；  
> 3. 若指定了 COLLATE collation_name 但未指定 CHARACTER SET，则使用与 collation_name 关联的字符集和指定的字符序；  
> 4. 若既不指定 CHARACTER SET 也不指定 COLLATE，则使用数据库的字符集和字符序；  
  
##### 2.3 列级字符集和字符序  
每个"字符"列（即类型为 CHAR、VARCHAR、TEXT 类型或任何同义词的列）都有一个列字符集和一个列字符序；  
```sql  
-- 创建表时指定列的字符集和字符序，语法如下：  
CREATE TABLE table_name (  
    column_name {CHAR | VARCHAR | TEXT} (column_length)  
    [CHARACTER SET charset_name]  
    [COLLATE collation_name]  
);  
  
-- 创建表并指定 col1 列的字符集和字符序  
CREATE TABLE t (col1 VARCHAR(5) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci);  
  
-- 查看表的定义  
obclient> SHOW CREATE TABLE test;  
```  


###### 2.3.1 选择列的字符集和字符序  

> [!NOTE] OceanBase 数据库通过以下方式选择列的字符集和字符序：  
> 1. 若在各个列定义中未指定列字符集和字符序，则表的字符集和字符序将用作列定义的默认值；  
> 2. 若同时指定了列的字符集和字符序，则使用字符集 charset_name 和字符序 collation_name；  
> 3. 若指定了列的字符集，但未指定 COLLATE，则使用字符集 charset_name 及其默认字符序；
> 	1. 要查看每个字符集的默认字符序，请使用 SHOW CHARACTER SET 语句；  
> 4. 若指定了 COLLATE collation_name 但未指定 CHARACTER SET，则使用与 collation_name 关联的字符集和指定的字符序；  
> 5. 若既不指定 CHARACTER SET 也不指定 COLLATE，则使用表的字符集和字符序；  


###### 2.3.2 列字符集转换条件  
要将二进制或非二进制字符串列转换为指定字符集，可以使用 ALTER TABLE；  

> [!NOTE] 要成功转换，必须满足以下条件之一：  
> 1. 如果列具有二进制数据类型（BINARY、VARBINARY 或 BLOB），则它包含的所有值都必须使用单个字符集（即要将列转换为的字符集）进行编码。如果使用二进制列来存储多个字符集的信息，OceanBase 数据库无法知道哪些值使用哪个字符集，无法正确转换数据；  
> 2. 如果列具有非二进制数据类型（CHAR、VARCHAR 或 TEXT），则其内容应根据列字符集进行编码。如果列内容以不同的字符集进行编码，可以先将列转换为所使用二进制数据类型，然后再转换为具有所需字符集的非二进制列；  
  
##### 2.4 字符表达式字符集和字符序  
每个字符表达式都有一个字符集和一个字符序，详细：[字符表达式字符集和字符序_MySql_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357416)，；  
对于简单语句 SELECT 'string'，该字符表达式的默认字符集和字符序由 `character_set_connection` 和 `collation_connection` 系统变量定义；  
```sql  
--   
SELECT @@character_set_connection , @@collation_connection;  
  
--  
SELECT _utf8mb4'abc' COLLATE utf8mb4_unicode_ci;  
```  


##### 2.5 当前会话的字符集，字符序
详细情况请参考：[[15_OceanBase/03_OceanBase 高阶进阶/OceanBase 字符集与字符序#1.2 客户端连接的字符集，字符序\|OceanBase 字符集与字符序#1.2 客户端连接的字符集，字符序]]，；



### 参考文档：  
1. 字符集与字符序概述：[MySQL租户_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357410)，；  
2. [SET NAMES_Oracle租户_V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357393)，介绍设置当前会话的字符集以及字符集排序规则；