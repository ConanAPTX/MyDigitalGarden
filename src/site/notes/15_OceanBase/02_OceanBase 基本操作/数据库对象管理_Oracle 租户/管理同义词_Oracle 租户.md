---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理同义词_Oracle 租户/","dgPassFrontmatter":true}
---


### 管理同义词_Oracle 租户  
在 OceanBase 数据库中，同义词是一个租户数据库对象的别名。使用同义词通常是为了管理权限方便，因为同义词可以隐藏另外一个数据对象的所有者权限；  

#### 1 创建同义词  
可以使用 CREATE SYNONYM 语句来创建同义词；  
> 创建同义词时，注意事项：  
> 1. 如果在当前数据库或 Schema 下创建私有同义词，则当前用户需要具备 CREATE SYNONYM 权限；  
> 2. 如果在非当前数据库或 Schema 下创建私有同义词，则当前用户需要具备 CREATE ANY SYNONYM 权限；  
> 3. 如果创建公共同义词，则当前用户需要具备 CREATE PUBLIC SYNONYM 权限；  
> 4. 创建同义词的对象可以不存在，当前用户也不需要具有该对象的访问权限；  
  
```sql  
/* 创建同义词的语法格式如下：CREATE [ OR REPLACE ] [ PUBLIC ] SYNONYM [ schema. ]synonym FOR [ schema. ]object; */  
  
CREATE TABLE test(c1 int); -- 创建表  
-- 创建一个私有同义词  
CREATE SYNONYM s1 for test;  
INSERT INTO s1 VALUES(1);  
SELECT * FROM s1;  
  
-- 创建一个公共同义词  
CREATE PUBLIC SYNONYM syn_pub FOR test;  
SELECT * FROM syn_pub;  
```  

#### 2 删除同义词  
可以使用 DROP SYNONYM 语句来删除不需要使用的同义词；  
若删除的是私有同义词，则需要保证待删除的同义词在对应的数据库或 Schema 下，且当前用户需要具备 DROP ANY SYNONYM 权限；  
若删除的是公共同义词，则当前用户需要具备 DROP PUBLIC SYNONYM 权限。同时，在删除时，必须指定 PUBLIC 关键字，并且不能指定数据库或 Schema；  
```sql  
-- 删除同义词的语法格式如下：DROP [PUBLIC] SYNONYM [ schema. ]synonym;  
  
DROP SYNONYM test.s1; -- 删除一个私有同义词  
  
DROP PUBLIC SYNONYM syn_pub; -- 删除一个公共同义词  
```  


### 参考文档：  
1. [管理同义词_4.2.1_Oracle](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218608)，；



