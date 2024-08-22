---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/导数工具/obloader  命令使用示例_01/","dgPassFrontmatter":true}
---


### obloader  命令使用示例_01
#### 1 导入 DDL 定义文件
##### 1.1 注意事项
> [!NOTE] `说明`：
> `--sys-user` 选项用于连接 sys 租户下拥有特定权限的用户。导入时如果未指定 `--sys-user` 选项时，默认指定的是 `--sys-user root`。

> [!NOTE] 导入表结构，注意事项：
> -   导入表结构时，SQL 内容是 DDL，使用 `--ddl` 选项。要求 SQL 里不能有注释和 SET 开关语句等；
> -   SQL 文件的目录要在 `-f` 选项中的 `/data/TABLE` 下，例如：`/home/admin/test_sql/data/TABLE/test_tbl1-schema.sql`；
> -   SQL 文件的名称格式：`表名-schema.sql`；
> -   导入数据时，需要指定 `--sys-user` 和 `--sys-password` 选项，OBLOADER 获取表结构元数据信息；

```shell
## 创建 SQL 文件
mkdir -p /home/admin/test_sql/data/TABLE
cd /home/admin/test_sql/data/TABLE
vi test_tbl1-schema.sql
cat test_tbl1-schema.sql
CREATE TABLE IF NOT EXISTS test_tbl1(col1 INT,col2 VARCHAR(50),PRIMARY KEY (col1));
```


##### 1.2 导入表结构
`场景描述`：将 `/output` 目录下所有已支持的数据库对象定义导入到 Schema USERA 中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）；
```shell
## 导入表结构，导出 DDL 定义文件
cd /home/admin/ob-loader-dumper-3.3.2-SNAPSHOT/bin
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --ddl --all -f /output
./obloader -h xx.x.x.x -P 2883 -u test -p ****** --sys-user **u*** --sys-password ****** -c cluster_a -t mysql -D USERA --ddl --all -f /output
```

`场景描述`：将 */home/admin/test_sql/data/TABLE* 下的 SQL 数据文件，导入到集群 test4000 下的租户 mysql001 里的 Schema test_data 中；
```shell
./obloader -h xxx.xxx.xxx.1 -P 2883 -u obloader_user01 -p ****** --sys-user root --sys-password ****** -c test4000 -t mysql001 -D test_data --ddl --all -f /home/admin/test_sql
```

```shell
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

任务结果示例：
```shell
All Load Tasks Finished:
---------------------------------------------------------
|  No.#  | Type   | Name       | Count       | Status   | 
---------------------------------------------------------     
|  1     | TABLE  | table      | 1 -> 1      | SUCCESS  |                
---------------------------------------------------------
Total Count: 1          End Time: 2023-05-11 16:02:08
```

#### 2 导入 CSV 数据文件

#### 3 导入 SQL 数据文件

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




#### 4 导入 POS 数据文件

#### 5 导入 CUT 数据文件

#### 6 从 Amazon S3 导入数据文件到 OceanBase 数据库

#### 7 从 Aliyun OSS 导入数据文件到 OceanBase 数据库

#### 8 从 Apache Hadoop 导入数据文件到 OceanBase 数据库

#### 9 云数据库 OceanBase 导入模式和数据

#### 10 OceanBase 数据库导入模式和数据


### 参考文档
1. *obloader 快速入门*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775418)，；


