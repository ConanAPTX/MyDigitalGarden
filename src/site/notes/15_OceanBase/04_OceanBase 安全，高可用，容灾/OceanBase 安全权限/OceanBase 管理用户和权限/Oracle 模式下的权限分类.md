---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/Oracle 模式下的权限分类/","dgPassFrontmatter":true}
---


### Oracle 模式下的权限分类

> [!NOTE] Oracle 模式的权限分为两类：
> *对象权限*：对特定对象的操作权限，例如：某个表对象的 Alter、Select、Update 等权限；
> *系统权限*：允许用户执行在一个 Schema 或者任何 Schema 上进行特定的数据库操作的权限；


#### 1 权限分类详细介绍
当前 OceanBase 数据库中 Oracle 模式相关的权限如下表所示:

##### 1.1 对象权限
*ALTER*：修改表结构的权限；
*INSERT*：向表、视图中插入数据的权限；
*UPDATE*：修改表、视图中数据的权限；
*DELETE*：删除表、视图中数据的权限；
*SELECT*：使用表、视图、同义词、序列的权限；
*INDEX*：给表加索引的权限；
*REFERENCE*：参考表的权限；
*EXECUTE*：执行存储过程、函数、系统包的权限；
*DEBUG*：调试程序的权限；
*READ*：读权限；赋予视图，表该权限会报错，提工单了解 Oracle 租户中没有该权限；
*WRITE*：写权限；赋予视图，表该权限会报错，提工单了解 Oracle 租户中没有该权限；

##### 1.2 系统权限
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


### 参考文档
1. [Oracle 模式下的权限分类](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220864)，；


