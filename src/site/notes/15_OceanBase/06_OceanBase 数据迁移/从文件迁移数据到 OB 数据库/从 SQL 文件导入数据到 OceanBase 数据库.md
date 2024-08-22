---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/从文件迁移数据到 OB 数据库/从 SQL 文件导入数据到 OceanBase 数据库/","dgPassFrontmatter":true}
---


### 从 SQL 文件导入数据到 OceanBase 数据库

将 SQL 格式的数据文件导入到 OceanBase 数据库，可以采用命令行界面导入和工具导入 2 种方式；OceanBase 数据库支持多种工具导入 SQL 格式的数据文件，如：`MySQLDumper`、`OBLOADER` 和 `OceanBase 开发者工具`（ODC）等图形化界面工具；

本文将主要介绍使用`命令行界面`，`OBLOADER`，`ODC` 从 SQL 文件导入数据到 OceanBase 数据库；

#### 1 配置 SQL 文件信息

导入到 OceanBase 数据库 SQL 文件中的 SQL 语法必须符合 OceanBase 数据库的要求；

```shell
cat test_tbl1.sql
DROP TABLE IF EXISTS test_data.test_tbl1;

CREATE TABLE IF NOT EXISTS test_data.test_tbl1(col1 INT,col2 VARCHAR(50),PRIMARY KEY (col1));

INSERT INTO test_data.test_tbl1 VALUES(1,'test1');
INSERT INTO test_data.test_tbl1 VALUES(2,'test2');
INSERT INTO test_data.test_tbl1 VALUES(3,'test3');
INSERT INTO test_data.test_tbl1 VALUES(4,'test4');
INSERT INTO test_data.test_tbl1 VALUES(5,'test5');
```

#### 2 使用命令行界面导入数据

```shell
# 使用 SQL 文件所在的机器登录 OceanBase 数据库
obclient -hxxx.xxx.xxx.1 -P2881 -uroot@mysql001 -p -A

# 创建 Schema 库 test_data
CREATE DATABASE test_data;      
SOURCE /home/admin/test_tbl1.sql   # 使用 SOURCE 命令导入 SQL 文件数据

# 查看导入数据信息
use test_data;
SHOW TABLES;
SELECT * FROM test_tbl1;
```


#### 3 使用 OBLOADER 导入数据
##### 3.1 准备 OBLOADER 运行环境
OBLOADER 是一个命令行压缩包，用户只需在宿主机上解压即可运行该工具；安装 OBLOADER 详请： [[15_OceanBase/06_OceanBase 数据迁移/导数工具/安装 OB 导数工具_V4.3.0\|安装 OB 导数工具_V4.3.0]]，；

##### 3.2 obloader 导入表结果，数据
[[15_OceanBase/06_OceanBase 数据迁移/导数工具/obloader  命令使用示例_01#1 导入 DDL 定义文件\|obloader  命令使用示例_01#1 导入 DDL 定义文件]]，；
[[15_OceanBase/06_OceanBase 数据迁移/导数工具/obloader  命令使用示例_01#3 导入 SQL 数据文件\|obloader  命令使用示例_01#3 导入 SQL 数据文件]]，；

#### 4 使用 ODC 导入数据
详细情况请看参考文档；


### 参考文档
1. *从 SQL 文件导入数据到 OceanBase 数据库*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000217937)，[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049776)，；



