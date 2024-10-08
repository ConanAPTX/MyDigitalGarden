---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/OceanBase 数据库的权限分类/","dgPassFrontmatter":true}
---


## OceanBase 数据库的权限分类
### 1 MySQL 模式下的权限分类
#### 1.1 权限分类概述

> [!NOTE] MySQL 模式的权限分为了 3 个级别：
> *管理权限*：可以影响整个租户的权限，例如：修改系统设置、访问所有的表等权限；
> *数据库权限*：可以影响某个特定数据库下所有对象的权限，例如：在对应数据库下创建删除表，访问表等权限；
> *对象权限*：可以影响某个特定对象的权限，例如：访问一个特定的表、视图或索引的权限；

#### 1.2 权限分类详细介绍
##### 1.2.1 对象权限
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


##### 1.2.2 数据库权限 	
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

##### 1.2.3 管理权限
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


### 2 Oracle 模式下的权限分类

> [!NOTE] Oracle 模式的权限分为两类：
> *对象权限*：对特定对象的操作权限，例如：某个表对象的 Alter、Select、Update 等权限；
> *系统权限*：允许用户执行在一个 Schema 或者任何 Schema 上进行特定的数据库操作的权限；


#### 2.1 权限分类详细介绍
当前 OceanBase 数据库中 Oracle 模式相关的权限如下表所示:

##### 2.1.1 对象权限
*ALTER*：修改表结构的权限；
*INSERT*：向表、视图中插入数据的权限；
*UPDATE*：修改表、视图中数据的权限；
*DELETE*：删除表、视图中数据的权限；
*SELECT*：使用表、视图、同义词、序列的权限；
*INDEX*：给表加索引的权限；
*REFERENCE*：参考表的权限；
*EXECUTE*：执行存储过程、函数、系统包的权限；
*DEBUG*：调试程序的权限；
*READ*：读权限；
*WRITE*：写权限；

##### 2.1.2 系统权限
*CREATE SESSION*：创建会话的权限；
*CREATE TABLE*：创建表的权限；
*CREATE ANY TABLE*：创建任意表的权限；
*ALTER ANY TABLE*：修改任意表的权限；
*DELETE ANY TABLE*：删除任意表数据的权限；
*DROP ANY TABLE*：删除任意表的权限；
*BACKUP ANY TABLE*：备份任意表的权限；
*LOCK ANY TABLE*：给任意表加锁的权限；
*COMMENT ANY TABLE*：为任意表添加注释的权限；
*INSERT ANY TABLE*：向任意表中插入行的权限；
*SELECT ANY TABLE*：使用任意表的权限；
*FLASHBACK ANY TABLE*：闪回任意表的权限；
*UPDATE ANY TABLE*：修改任意表中的行的权限；
*CREATE ROLE*：创建角色的权限；
*DROP ANY ROLE*：删除任意角色的权限；
*GRANT ANY ROLE*：授予任意角色的权限；
*ALTER ANY ROLE*：修改任意角色的权限；
*AUDIT ANY*：为任意对象设置审计选项的权限；
*GRANT ANY PRIVILEGE*：授予用户任意系统权限的权限；
*GRANT ANY OBJECT PRIVILEGE*：授予用户任意的权限；
*CREATE ANY INDEX*：创建任意索引的权限；
*ALTER ANY INDEX*：修改任意索引的权限；
*DROP ANY INDEX*：删除任意索引的权限；
*CREATE VIEW*：创建视图的权限；
*CREATE ANY VIEW*：创建任意视图的权限；
*DROP ANY VIEW*：删除任意视图的权限；
*SELECT ANY DICTIONARY*：使用任意数据字典的权限；
*CREATE PROCEDURE*：为用户创建存储过程的权限；
*CREATE ANY PROCEDURE*：为用户创建任意存储过程的权限；
*ALTER ANY PROCEDURE*：修改任意存储过程的权限；
*DROP ANY PROCEDURE*：删除任意存储过程的权限；
*EXECUTE ANY PROCEDURE*：执行任意存储过程的权限；
*CREATE SYNONYM*：为用户创建同义词的权限；
*CREATE ANY SYNONYM*：为用户创建任意同义词的权限；
*CREATE PUBLIC SYNONYM*：创建公共同义词的权限；
*DROP ANY SYNONYM*：删除任意同义词的权限；
*DROP PUBLIC SYNONYM*：删除公共同义词的权限；
*CREATE SEQUENCE*：为用户创建序列的权限；
*CREATE ANY SEQUENCE*：创建任意序列的权限；
*ALTER ANY SEQUENCE*：修改任意序列的权限；
*DROP ANY SEQUENCE*：删除任意序列的权限；
*SELECT ANY SEQUENCE*：使用任意序列的权限；
*CREATE TRIGGER*：为用户创建触发器的权限；
*CREATE ANY TRIGGER*：为用户创建任意触发器的权限；
*ALTER ANY TRIGGER*：修改任意触发器的权限；
*DROP ANY TRIGGER*：删除任意触发器的权限；
*CREATE PROFILE*：创建资源限制简表的权限；
*ALTER PROFILE*：修改资源限制简表的权限；
*DROP PROFILE*：删除资源限制简表的权限；
*CREATE USER*：创建用户的权限；
*ALTER USER*：修改用户的权限；
*DROP USER*：删除用户的权限；
*BECOME USER*：切换用户状态的权限；
*CREATE TYPE*：创建类型的权限；
*CREATE ANY TYPE*：创建任意类型的权限；
*ALTER ANY TYPE*：修改任意类型的权限；
*DROP ANY TYPE*：删除任意类型的权限；
*EXECUTE ANY TYPE*：执行任意类型的权限；
*PURGE DBA_RECYCLEBIN*：清除回收站的权限；
*CREATE ANY OUTLINE*：创建任意执行计划的权限；
*ALTER ANY OUTLINE*：修改任意执行计划的权限；
*DROP ANY OUTLINE*：删除任意执行计划的权限；
*CREATE TABLESPACE*：创建表空间的权限；
*ALTER TABLESPACE*：修改表空间的权限；
*DROP TABLESPACE*：删除表空间的权限；
*SHOW PROCESS*：查看所有用户线程的权限；
*ALTER SYSTEM*：修改服务器设置的权限；
*CREATE DATABASE LINK*：创建数据库链接的权限；
*CREATE PUBLIC DATABASE LINK*：创建公共数据库链接的权限；
*DROP DATABASE LINK*：删除数据库链接的权限；
*ALTER SESSION*：修改会话的权限；
*ALTER DATABASE*：修改数据库的权限；
*CREATE ANY DIRECTORY*：创建任意目录的权限；
*DROP ANY DIRECTORY*：删除任意目录的权限；
*DEBUG CONNECT SESSION*：调试连接会话的权限；
*DEBUG ANY PROCEDURE*：调试任何程序的权限；
*CREATE ANY CONTEXT*：创建任意上下文的权限；
*DROP ANY CONTEXT*：删除任意上下文的权限；


## 参考文档
1. [MySQL 模式下的权限分类](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220876)，；
2. [Oracle 模式下的权限分类](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220864)，；


