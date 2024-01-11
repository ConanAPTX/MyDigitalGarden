---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/OceanBase 数据库 MySQL 模式下的权限分类/","dgPassFrontmatter":true}
---


### OceanBase 数据库 MySQL 模式下的权限分类
#### 1 权限分类概述

> [!NOTE] MySQL 模式的权限分为了 3 个级别：
> *管理权限*：可以影响整个租户的权限，例如：修改系统设置、访问所有的表等权限；
> *数据库权限*：可以影响某个特定数据库下所有对象的权限，例如：在对应数据库下创建删除表，访问表等权限；
> *对象权限*：可以影响某个特定对象的权限，例如：访问一个特定的表、视图或索引的权限；

#### 2 权限分类详细介绍
##### 2.1 对象权限
1. *CREATE*：确定用户是否可以创建表；
2. *SELECT*：确定用户是否可以查询表中的数据；
3. *INSERT*：确定用户是否可以在表中插入行数据；
4. *UPDATE*：确定用户是否可以修改现有数据；
5. *DELETE*：确定用户是否可以删除现有数据；
6. *DROP*：确定用户是否可以删除现有表和视图；
7. *INDEX*：确定用户是否可以创建和删除表索引；
8. *ALTER*：确定用户是否可以重命名和修改表结构；
9. *CREATE VIEW*：确定用户是否可以创建视图；
10. *SHOW VIEW*：确定用户是否可以查看视图或了解视图如何执行；


##### 2.2 数据库权限 	
1. *SELECT*：确定用户是否可以查询表中的数据；
2. *INSERT*：确定用户是否可以在表中插入行数据；
3. *UPDATE*：确定用户是否可以修改现有数据；
4. *DELETE*：确定用户是否可以删除现有数据；
5. *CREATE*：确定用户是否可以创建数据库和表；
6. *DROP*：确定用户是否可以删除现有数据库、表和视图；
7. *INDEX*：确定用户是否可以创建和删除表索引；
8. *ALTER*：确定用户是否可以重命名和修改表结构；
9. *CREATE VIEW*：确定用户是否可以创建视图；
10. *SHOW VIEW*：确定用户是否可以查看视图或了解视图如何执行；

##### 2.3 管理权限
1. *CREATE*：确定用户是否可以创建数据库、表组和表；
2. *ALTER*：确定用户是否可以修改表组、重命名和修改表结构；
3. *SELECT*：确定用户是否可以查询表中的数据；
4. *INSERT*：确定用户是否可以在表中插入行数据；
5. *UPDATE*：确定用户是否可以修改现有数据；
6. *DELETE*：确定用户是否可以删除现有数据；
7. *DROP*：确定用户是否可以删除现有数据库、表组、表和视图；
8. *INDEX*：确定用户是否可以创建和删除表索引；
9. *CREATE VIEW*：确定用户是否可以创建视图；
10. *SHOW VIEW*：确定用户是否可以查看视图或了解视图如何执行；
11. *ALTER TENANT*：修改租户信息的权限；
12. *ALTER SYSTEM*：执行 ALTER SYSTEM 命令的权限；
13. *CREATE RESOURCE POOL*：创建、修改和删除资源池的权限；
14. *CREATE RESOURCE UNIT*：创建、修改和删除资源单元的权限；
15. *CREATE DATABASE LINK*：创建数据库链接的权限；
16. *DROP DATABASE LINK*：删除数据库链接的权限；
17. *CREATE USER*：确定用户是否可以执行 CREATE USER 命令，这个命令用于创建新的 MySQL 账号；
18. *PROCESS*：确定用户是否可以通过 SHOW PROCESSLIST 命令查看其他用户的进程；
19. *SHOW DB*：确定用户是否可以查看服务器上所有数据库的名字，包括用户拥有足够访问权限的数据库；
20. *FILE*：确定用户是否可以执行 SELECT INTO OUTFILE 和 LOAD DATA INFILE 命令；
21. *SUPER*：确定用户是否可以执行某些强大的管理功能，例如通过 KILL 命令删除用户进程，使用 SET GLOBAL 修改全局 MySQL 变量，执行关于复制和日志的各种命令；


### 参考文档
1. [MySQL 模式下的权限分类](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220876)，；


