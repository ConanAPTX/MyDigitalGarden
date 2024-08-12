---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理数据库_MySql 模式/","dgPassFrontmatter":true}
---


#### 1 查看数据库  
```sql  
-- 查看数据库  
SHOW DATABASES;  
```  


#### 2 创建，删除数据库
更多 CREATE DATABASE 语句相关的语法说明请参见 [CREATE DATABASE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946726)，；  
若误删数据库，可以通过回收站功能进行恢复，前提是需要开启回收站功能；更多 DROP DATABASE 语句相关的语法说明请参见 [DROP DATABASE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945536)，； 

```sql  
-- 创建数据库
CREATE DATABASE test2 DEFAULT CHARACTER SET UTF8; -- 创建数据库 test2，并指定字符集为 UTF8  
CREATE DATABASE test3 READ WRITE; -- 创建读写属性的数据库 test3  
CREATE DATABASE test4 READ ONLY; -- 创建只读属性的数据库 test4  
CREATE TABLE new_database_name.table AS SELECT * FROM old_database_name.table;  -- 复制表结构；  

-- 删除数据库
DROP DATABASE my_db; -- 删除数据库
```  
 

#### 3 修改数据库
更多 ALTER DATABASE 语句相关的语法说明请参见 [ALTER DATABASE](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946718)，；  

```sql  
-- 修改数据库 test2 的字符集为 UTF8MB4，字符序为 UTF8MB4_BIN，且为读写属性；  
ALTER DATABASE test2 DEFAULT CHARACTER SET UTF8MB4;  
ALTER DATABASE test2 DEFAULT COLLATE UTF8MB4_BIN;  
ALTER DATABASE test2 READ WRITE;  
```  
```sql  
-- 修改数据库注释和名称(使用数据库管理员账号登录到 OceanBase 数据库)  
-- 修改注释，语法：ALTER DATABASE `旧数据库名` COMMENT '新注释';  
ALTER DATABASE test2 SET COMMENT 'comment';  
  
-- 修改名称：  
RENAME DATABASE `旧数据库名` TO `新数据库名`;  
ALTER DATABASE old_database_name RENAME TO new_database_name; -- 修改名称  
```  


### 参考文档  
1. 