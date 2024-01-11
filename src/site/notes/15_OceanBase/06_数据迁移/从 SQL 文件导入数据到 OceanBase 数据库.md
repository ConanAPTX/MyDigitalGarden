---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_数据迁移/从 SQL 文件导入数据到 OceanBase 数据库/","dgPassFrontmatter":true}
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
OBLOADER 是一个命令行压缩包，用户只需在宿主机上解压即可运行该工具；安装 OBLOADER 详请： [[15_OceanBase/01_部署 OceanBase 数据库/安装导数工具 OBLOADER\|安装导数工具 OBLOADER]]，；

##### 3.2 导入表结构

> [!NOTE] 导入表结构，注意事项：
> -   导入表结构时，SQL 内容是 DDL，使用 `--ddl` 选项。要求 SQL 里不能有注释和 SET 开关语句等；
> -   SQL 文件的目录要在 `-f` 选项中的 `/data/TABLE` 下，例如：`/home/admin/test_sql/data/TABLE/test_tbl1-schema.sql`；
> -   SQL 文件的名称格式：`表名-schema.sql`；
> -   导入数据时，需要指定 `--sys-user` 和 `--sys-password` 选项，OBLOADER 获取表结构元数据信息；


```shell
# 场景描述：将 /home/admin/test_sql/data/TABLE 下的 SQL 数据文件，导入到集群 test4000 下的租户 mysql001 里的 Schema test_data 中
## 创建 SQL 文件
mkdir -p /home/admin/test_sql/data/TABLE
cd /home/admin/test_sql/data/TABLE
vi test_tbl1-schema.sql
cat test_tbl1-schema.sql
CREATE TABLE IF NOT EXISTS test_tbl1(col1 INT,col2 VARCHAR(50),PRIMARY KEY (col1));

## 导入表结构
cd /home/admin/ob-loader-dumper-3.3.2-SNAPSHOT/bin
./obloader -h xxx.xxx.xxx.1 -P 2883 -u obloader_user01 -p ****** --sys-user root --sys-password ****** -c test4000 -t mysql001 -D test_data --ddl --all -f /home/admin/test_sql
<<xxxx
	-h(--host)		是，连接 ODP 或者 OceanBase 物理节点的主机地址；
	-P(--port)		是，连接 ODP 或者 OceanBase 物理节点的主机端口；
	-u(--user) 		是，数据库用户名； 		
	-p(--password)	否，数据库密码；
	--sys-user		否，指定 sys 租户下用户名；
	--sys-password	否，指定 sys 租户下特定用户的密码；
	-c(--cluster)	否，数据库的集群名； 		
	-t(--tenant)	否，集群的租户名；		
	-D(--database)	否，数据库名；
	--ddl 			否，导入 DDL 文件；
	--sql 			否，导入 SQL 格式的数据文件，区别于 DDL 文件；
	--all 			否，导入所有已支持的数据库对象定义和表数据；
	-f(--file-path)	是，数据文件所在的目录或者数据文件绝对路径；
xxxx
```


##### 3.3 导入表数据
> [!NOTE] 导入表数据，注意事项：
> -   导入表数据时，SQL 内容是 DML，使用 `--sql` 选项。SQL 文件中的内容仅包含 `INSERT` 语句, 数据不换行；
> -   SQL 文件的目录要在 `-f` 选项中的 `data` 下，例如：`/home/admin/test_sql/data/test_tbl1.sql`；
> -   SQL 文件的名称格式：`表名.sql`；
> -   数据库（Schema）下要存在 SQL 文件对应的表名；

```shell

# 场景描述：将 /home/admin/test_sql/data/ SQL 数据文件，导入到集群 test4000 下的租户 mysql001 里的 Schema test_data 中
## 创建 SQL 文件
mkdir -p /home/admin/test_sql/data
cd /home/admin/test_sql/data
vi test_tbl1.sql
cat test_tbl1.sql
INSERT INTO test_tbl1 VALUES(1,'test1');
INSERT INTO test_tbl1 VALUES(2,'test2');
INSERT INTO test_tbl1 VALUES(3,'test3');
INSERT INTO test_tbl1 VALUES(4,'test4');
INSERT INTO test_tbl1 VALUES(5,'test5');

## 导入表数据
cd /home/admin/ob-loader-dumper-3.3.2-SNAPSHOT/bin
./obloader -h xxx.xxx.xxx.1 -P 2883 -u obloader_user01 -p ****** --sys-user root --sys-password ****** -c test4000 -t mysql001 -D test_data --sql --all -f /home/admin/test_sql
<<xxxx
	-h(--host)		是，连接 ODP 或者 OceanBase 物理节点的主机地址；
	-P(--port)		是，连接 ODP 或者 OceanBase 物理节点的主机端口；
	-u(--user) 		是，数据库用户名； 		
	-p(--password)	否，数据库密码；
	--sys-user		否，指定 sys 租户下用户名；
	--sys-password	否，指定 sys 租户下特定用户的密码；
	-c(--cluster)	否，数据库的集群名； 		
	-t(--tenant)	否，集群的租户名；		
	-D(--database)	否，数据库名；
	--ddl 			否，导入 DDL 文件；
	--sql 			否，导入 SQL 格式的数据文件，区别于 DDL 文件；
	--all 			否，导入所有已支持的数据库对象定义和表数据；
	-f(--file-path)	是，数据文件所在的目录或者数据文件绝对路径；	
xxxx
```


#### 4 使用 ODC 导入数据
详细情况请看参考文档；


### 参考文档
1. [从 SQL 文件导入数据到 OceanBase 数据库](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000217937)，；



