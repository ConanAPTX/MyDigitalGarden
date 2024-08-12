---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/导数工具：obloader，obdumper/obdumper 命令使用示例/","dgPassFrontmatter":true}
---


### obdumper 命令使用示例

#### 1 导出 DDL 定义文件
**场景描述**：将 Schema USERA 中所有已支持的对象定义语句导出到 `/output` 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）;

**示例语句**：
```shell
# 导出 DDL 定义文件
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --ddl --all -f /output
	# 将 Schema USERA 中所有已支持的对象定义语句导出到 /output 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）。
```

**任务结果示例**：
```shell
...
All Dump Tasks Finished:
---------------------------------------------------
|  No.#  | Type   | Name     | Count   | Status   |  
---------------------------------------------------     
|  1     | TABLE  | table    | 1->1    | SUCCESS  |                
---------------------------------------------------

Total Count: 4          End Time: 2023-04-28 15:32:49
...
```

**导出内容示例**：
在 `{ob-loader-dumper}/output/data/chz/TABLE` 目录下查看 `table-schema.sql` 表。
```shell
[xxx@xxx /ob-loader-dumper-4.2.0-RELEASE/bin]
$cat /home/admin/obloaderobdumper/output/data/chz/TABLE/table-schema.sql
create table if not exists `table` (
        `id` int(11) comment 'table id',
        `name` varchar(120) comment 'table name',
        `type` varchar(128) not null default 'COLLABORATION' comment 'organization type, enum values: COLLABORATION, PRIVACY'
)
default charset=gbk
default collate=gbk_chinese_ci;
```

#### 2 导出 DDL 定义文件_未完成

#### 3 导出 CSV 数据文件_未完成

#### 4 导出 SQL 数据文件_未完成

#### 5 导出 CUT 数据文件_未完成

#### 6 导出数据文件到 Amazon S3_未完成

#### 7 导出数据文件到 Aliyun OSS_未完成


#### 8 自定义导出文件名

**场景描述**： 将 Schema USERA 中 table 表的数据按照 CSV 格式导出到 `/output` 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）。同时指定导出的文件名称为 filetest；

**示例语句**：
```shell
### 自定义导出文件名
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --csv --table 'table' -f filetest.txt
	# 将 Schema USERA 中 table 表的数据按照 CSV 格式导出到 /output 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）。
	# 同时指定导出的文件名称为 filetest；
```

> [!NOTE] *说明*：
> OBDUMPER 4.2.7 及之后版本，导出单个文件时支持指定 `-f` 为具体的文件名；

**任务结果示例**
```shell
...
All Dump Tasks Finished:
----------------------------------------------------
|  No.#  | Type   | Name       | Count  | Status   | 
----------------------------------------------------      
|  1     | TABLE  | table      | 4      | SUCCESS  |                
----------------------------------------------------

Total Count: 4          End Time: 2023-04-28 15:32:49
...
```

**导出内容示例**：

查看 `filetest.txt` 表。
```shell
[xxx@xxx /ob-loader-dumper-4.2.0-RELEASE/bin]
$cat filetest.txt
1001,'xiaoning','COLLABORATION'
1002,'xiaohong','COLLABORATION'
1001,'xiaoning','COLLABORATION'
1002,'xiaohong','COLLABORATION'
```


#### 9 导出数据时使用控制文件_未完成

#### 10 导出表中指定列的数据_未完成

#### 11 导出自定义查询结果集_未完成

#### 12 云数据库 OceanBase 导出模式和数据_未完成

#### 13 OceanBase 数据库导出模式和数据_未完成

### 参考文档
1. *obdumper 快速入门*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775409)，；


