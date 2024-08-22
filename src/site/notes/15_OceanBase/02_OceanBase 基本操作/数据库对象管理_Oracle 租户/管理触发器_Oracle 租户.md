---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理触发器_Oracle 租户/","dgPassFrontmatter":true}
---


### 管理触发器_Oracle 租户
#### 1 触发器概述  
触发器是 OceanBase 数据库中为 Oracle 租户提供的功能，它类似过程和函数，是包含声明、执行和异常处理过程的 PL 块；触发器基于特定事件被执行，可以指定触发器是在事件之前还是之后触发，以及触发器是针对单个事件还是被事件执行影响的某行数据；  

触发器是一个独立的对象，当某个事件发生时自动地隐式运行，而且触发器不能接收参数。这里的事件指的是对数据库的表进行的 INSERT、UPDATE 及 DELETE 操作；  

##### 1.1 触发器的优点
正确使用触发器可以使应用程序的构建和部署更加简单且健壮；

您可以通过触发器强制所有客户端程序执行底层业务逻辑。例如，多个客户端应用程序都访问某张表，如果这个表上的触发器保证了插入数据需要执行的逻辑，那么这个业务逻辑就不需要在每个客户端都执行，由于应用程序无法绕过触发器，因此会自动使用触发器中的业务逻辑；

请在需要的情况下使用触发器，避免触发器过度使用。过度使用触发器会导致复杂的互相依赖关系，在大型应用程序中会变得难以维护。例如，当触发器被触发时，其中执行的 SQL 语句可能会触发其他的触发器，从而导致级联触发，可能会产生预期之外的结果；

##### 1.2 触发器的分类  
触发器可以是简单的，也可以是组合的。OceanBase 数据库当前主要支持以下几种 DML 触发器：`行级触发器`，`语句级触发器`，`INSTEAD OF 触发器`，`组合触发器`；  

##### 1.3 触发器类型

OceanBase 数据库可以创建以下几种类型触发器：

`行级触发器`：行级触发器创建在实体表上，每次表受到触发语句影响时会触发一个行级触发器。例如，一条语句更新多行数据，每条受影响的数据都会触发一次触发器。如果触发语句不影响任何行数据，则不会运行触发器；

`语句级触发器`：语句级触发器创建在实体表上，每次执行触发语句时都会自动触发一次，无论该触发语句是否影响了表中的任何行数据。例如，一条语句更新了表中的 100 条数据，则语句级 `UPDATE` 触发器仅触发一次；

`INSTEAD OF 触发器`：INSTEAD OF 触发器创建在视图上，当对视图执行触发语句时自动触发。INSTEAD OF 触发器可以用来修改无法直接通过 DML 语句修改的视图；
`INSTEAD OF 触发器`：INSTEAD OF 触发器可以创建在可编辑视图与不可编辑视图上，用于更新视图；

`组合触发器`：一个组合触发器中可以包含行前、行后、语句前、语句后四种触发事件中的一到四种或只包含一个 INSTEAD OF 触发事件；
`组合 DML 触发器`：组合 DML 触发器可以创建于表或视图上，支持在多个时间点触发。组合触发器按照每个时间点分隔成段，每个时间段都包含独立的可执行部分和异常处理部分（可选的）；

> [!NOTE] `注意`：
> OceanBase 数据库 V2.2.7x 及以下版本`仅支持表上的行级触发器`；  


##### 1.4 触发器的触发时机  
您可以定义触发器的触发时机，触发时机是指触发操作在触发语句之前还是之后运行。

> [!NOTE] 行级触发器和语句级触发器可以指定如下触发时机：
> 1. 在触发语句执行之前；
> 2. 在每行数据被触发语句修改之前；
> 3. 在每行数据被触发语句修改之后；
> 4. 在触发语句执行之后；
    

对于语句级和行级触发器来说，BEFORE 触发器可以在对数据进行更改之前增强安全性并执行业务规则。AFTER 触发器非常适合用来记录操作日志。

> [!NOTE] 一个简单的触发器的触发时机有四个点：
> 1. 在事件执行之前：语句级别的 BEFORE 触发器；
> 2. 在事件执行之后：语句级别的 AFTER 触发器；
> 3. 在每行被事件执行影响之前：行级的 BEFORE 触发器；
> 4. 在每行被事件执行影响之后：行级的 AFTER 触发器；  


> [!NOTE] `触发器的触发顺序如下`：
> 1. 同一类型的触发器的执行顺序是不确定的，当前暂不支持指定触发器执行顺序；
> 2. 一个 DML 语句可能会触发多个简单的触发器，触发执行的先后顺序是：语句级 BEFORE 触发器 -> 行级 BEFORE 触发器 -> 行级 AFTER 触发器 -> 语句级 AFTER 触发器；


#### 2 查看触发器信息  
1. 查看触发器详细信息：*该内容仅适用于 OceanBase 数据库企业版。OceanBase 数据库社区版仅提供 MySQL 模式；*
	1. `USER_TRIGGERS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750370)，描述当前用户拥有的触发器；
	2. `DBA_TRIGGERS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750347)，描述数据库中的所有触发器；
	3. `ALL_TRIGGERS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750237)，描述当前用户拥有的表上触发器与描述。如果用户具有 CREATE ANY TRIGGER权限，则该视图描述数据库中的所有触发器；

```sql
-- 通过相关字典视图查看触发器详细信息，包括触发器的名称、类型、作用的表等
-- 列出当前用户所有的触发器，包括类型、状态和作用的表及其用户  
SELECT TRIGGER_NAME, TRIGGER_TYPE, TRIGGERING_EVENT, TABLE_OWNER, TABLE_NAME, STATUS FROM USER_TRIGGERS;  
  
-- 查看触发器 DEL_NEW_REGION 的定义内容  
SELECT TRIGGER_BODY FROM USER_TRIGGERS WHERE TRIGGER_NAME = 'DEL_NEW_REGION'\G  
```

1. 查看触发器相关信息：*该内容仅适用于 OceanBase 数据库企业版。OceanBase 数据库社区版仅提供 MySQL 模式；*
	1. `USER_SOURCE`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750318)，查看当前用户拥有的存储对象的文本源。其列与 `ALL_SOURCE` 中的列相同，去除了 `OWNER` 字段；
	2. `DBA_SOURCE`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750404)，查看数据库中所有存储对象的文本源。其列与 `ALL_SOURCE` 中的列相同；
	3. `ALL_SOURCE`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750178)，查看当前用户可以访问的存储对象的文本源；
2. 亦可通过查询数据库对象获取触发器信息：[[15_OceanBase/20_OceanBase 常用 Sql 语句/OceanBase 常用查询语句/Oceanase 查询数据库对象情况\|Oceanase 查询数据库对象情况]]，；
	1. `DBA_OBJECTS`：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000750204)，用于查看数据库所有数据库对象；`该视图从 V2.2.77 版本开始引入`； 
		1. *该视图仅适用于 OceanBase 数据库 Oracle 模式*；

```sql
-- OB 数据库用户可以通过以下视图查看触发器相关信息

SELECT NAME FROM USER_SOURCE WHERE TYPE='TRIGGER' GROUP BY NAME;  
  
SELECT NAME FROM DBA_SOURCE WHERE TYPE='TRIGGER';  
  
-- 用于查看数据库所有数据库对象(该视图仅适用于 OceanBase 数据库 Oracle 模式)
obclient [SYS]> SELECT * FROM SYS.DBA_OBJECTS WHERE ROWNUM <= 2;
SELECT OBJECT_NAME FROM DBA_OBJECTS WHERE OBJECT_TYPE='TRIGGER' AND OWNER='username';
```


#### 3 禁用，启用，删除触发器  
```sql
-- 1.禁用，启用触发器  
	-- 可以使用 ALTER TRIGGER 语句与 DISABLE 或 ENABLE 子句来禁用或启用触发器；  
ALTER TRIGGER trigger_name DISABLE;   -- 禁用触发器
ALTER TRIGGER trigger_name ENABLE;    -- 启用触发器

--2.删除触发器
	-- 注意事项：当删除表时，建立在该对象上的触发器也会随之删除；
DROP TRIGGER trigger_name; 
```

### 参考文档
1. *PL 概念 > Oracle 模式 > 触发器*：[V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000752694)，[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000643253)，Oracle 模式触发器概述；
2. [触发器概述_Oracle 租户_V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641875)，；
3. `Oracle 租户_V4.3.0`：[查看触发器信息_](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641870)，[禁用和启用触发器](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641876)，[修改和删除触发器](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641874)，；
