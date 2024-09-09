---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase  闪回查询多数据库版本/","dgPassFrontmatter":true}
---


### OceanBase 闪回查询多数据库版本

OceanBase 数据库提供了记录级别的闪回查询（Flashback Query）功能，该功能允许用户获取某个历史版本的数据；
其中，Oracle 模式支持 `AS OF SCN` 和 `AS OF TIMESTAMP` 两种语法来查询；MySQL 模式支持通过 `AS OF SNAPSHOT` 语法来查询；

#### 1 1.闪回查询支持的方式概述
OceanBase 数据库当前支持通过以下两种方式来进行闪回查询：【`创建 Restore Point` 】，【`设置 undo_retention 变量`】；

##### 1.1 闪回查询_创建 Restore Point
当用户创建了 Restore Point，则可使用闪回查询功能查询 Restore Point 点对应的数据版本；
Restore Point 的创建及使用的相关操作请参见： [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase Restore Point\|OceanBase Restore Point]]，；

##### 1.2 设置 undo_retention
OceanBase 数据库当前支持用户通过设置租户级配置项 `undo_retention` 的方式来进行闪回查询。当用户设置该配置项后，即可使用闪回查询功能查询当前时间 `T` 到 `T - undo_retention` 时间范围内的任意多版本数据；
详细情况：[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase 闪回查询_MySql，Oracle 模式\|OceanBase 闪回查询_MySql，Oracle 模式]]，；


### 参考文档：


