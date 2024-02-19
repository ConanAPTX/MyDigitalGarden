---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/闪回/OceanBase  闪回查询多数据库版本/","dgPassFrontmatter":true}
---


### OceanBase 闪回查询多数据库版本

OceanBase 数据库提供了记录级别的闪回查询（Flashback Query）功能，该功能允许用户获取某个历史版本的数据；
其中，Oracle 模式支持 `AS OF SCN` 和 `AS OF TIMESTAMP` 两种语法来查询；
MySQL 模式支持通过 `AS OF SNAPSHOT` 语法来查询；

#### 1 1.闪回查询支持的方式  
OceanBase 数据库当前支持通过以下两种方式来进行闪回查询：

##### 1.1 创建 Restore Point  
当用户创建了 Restore Point，则可使用闪回查询功能查询 Restore Point 点对应的数据版本；
Restore Point 的创建及使用的相关操作请参见： [[15_OceanBase/04_OceanBase 高可用和容灾/闪回/OceanBase Restore Point\|OceanBase Restore Point]]，；

##### 1.2 设置 undo_retention 变量
当用户设置 undo_retention 变量后，即可使用闪回查询功能查询当前时间 T 到 T - undo_retention 时间范围内的任意多版本数据；

#### 2 过设置 undo_retention 变量来进行闪回查询  

> [!NOTE] `注意事项`：
> 1. 假设 undo_retention 变量设置的时间为 T0，变量设置后对 T0 之前的数据不起作用，对 T0 之后的数据才起作用；
> 2. 如果被查询的表已经被删除进了回收站，则需要先将该表从回收站中恢复；
> 3. 假设当前时间为 T，设置 undo_retention 变量后，如果在 T - undo_retention 时间段内有 DDL 操作，系统会用最新的数据字典解释表的数据。对于 DDL 操作发生前产生的数据，被删除的列的数据将变成删除状态，新添加列的数据则设为 Default 值；



### 参考文档：
