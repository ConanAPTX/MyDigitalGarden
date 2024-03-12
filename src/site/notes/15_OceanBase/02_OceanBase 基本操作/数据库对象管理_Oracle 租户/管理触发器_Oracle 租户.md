---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理触发器_Oracle 租户/","dgPassFrontmatter":true}
---


### 管理触发器_Oracle 租户
#### 1 触发器概述  
触发器是 OceanBase 数据库中为 Oracle 租户提供的功能，它类似过程和函数，是包含声明、执行和异常处理过程的 PL 块。触发器基于特定事件被执行，可以指定触发器是在事件之前还是之后触发，以及触发器是针对单个事件还是被事件执行影响的某行数据；  
触发器是一个独立的对象，当某个事件发生时自动地隐式运行，而且触发器不能接收参数。这里的事件指的是对数据库的表进行的 INSERT、UPDATE 及 DELETE 操作；  

##### 1.1 触发器的触发时机  
一个简单的触发器的触发时机有四个点：1. 在事件执行之前：语句级别的 BEFORE 触发器；2. 在事件执行之后：语句级别的 AFTER 触发器；3. 在每行被事件执行影响之前：行级的 BEFORE 触发器；4. 在每行被事件执行影响之后：行级的 AFTER 触发器；  
一个 DML 语句可能会触发多个简单的触发器，触发执行顺序是：语句级 BEFORE 触发器 -> 行级 BEFORE 触发器 -> 行级 AFTER 触发器 -> 语句级 AFTER 触发器；  
同一类型的触发器的执行顺序是不确定的，当前暂不支持指定触发器的执行顺序；  
注意：OceanBase 数据库 V2.2.7x 及以下版本仅支持表上的行级触发器；  

##### 1.2 触发器的分类  
触发器可以是简单的，也可以是组合的。OceanBase 数据库当前主要支持以下几种 DML 触发器：行级触发器，语句级触发器，INSTEAD OF 触发器，组合触发器；  
INSTEAD OF 触发器：INSTEAD OF 触发器可以创建在可编辑视图与不可编辑视图上，用于更新视图；  
组合触发器：一个组合触发器中可以包含行前、行后、语句前、语句后四种触发事件中的一到四种或只包含一个 INSTEAD OF 触发事件；  

#### 2 查看触发器信息  
触发器详细信息可以通过相关字典视图来查看，包括触发器的名称、类型、作用的表等；  
`USER_TRIGGERS`：描述当前用户拥有的触发器；  `USER_TRIGGERS`：描述当前用户拥有的触发器；  
`DBA_TRIGGERS`：描述数据库中的所有触发器；  
`ALL_TRIGGERS`：描述当前用户拥有的表上触发器与描述。如果用户具有 CREATE ANY TRIGGER 权限，则该视图描述数据库中的所有触发器；  

```sql
-- 列出当前用户所有的触发器，包括类型、状态和作用的表及其用户  
SELECT TRIGGER_NAME, TRIGGER_TYPE, TRIGGERING_EVENT, TABLE_OWNER, TABLE_NAME, STATUS FROM USER_TRIGGERS;  
  
-- 查看触发器 DEL_NEW_REGION 的定义内容  
SELECT TRIGGER_BODY FROM USER_TRIGGERS WHERE TRIGGER_NAME = 'DEL_NEW_REGION'\G  
```

OB 数据库用户还可以通过 `DBA_SOURCE`、`USER_SOURCE` 和 `DBA_OBJECTS` 视图查看触发器相关信息；  

```sql
SELECT NAME FROM USER_SOURCE WHERE TYPE='TRIGGER' GROUP BY NAME;  
  
SELECT NAME FROM DBA_SOURCE WHERE TYPE='TRIGGER';  
  
SELECT OBJECT_NAME FROM DBA_OBJECTS WHERE OBJECT_TYPE='TRIGGER' AND OWNER='username';  
```


#### 3 禁用和启用触发器  
```sql
可以使用 ALTER TRIGGER 语句与 DISABLE 或 ENABLE 子句来禁用或启用触发器；  
-- 禁用触发器  
ALTER TRIGGER trigger_name DISABLE;  
  
-- 启用触发器  
ALTER TRIGGER trigger_name ENABLE;  
```


#### 4 删除触发器  

```sql
当删除表时，建立在该对象上的触发器也会随之删除；  
DROP TRIGGER trigger_name;
```



### 参考文档



