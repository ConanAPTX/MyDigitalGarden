---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/导数工具：obloader，obdumper/obloader  命令使用示例/","dgPassFrontmatter":true}
---


### obloader  命令使用示例
#### 1 导入 DDL 定义文件
**场景描述**：将 `/output` 目录下所有已支持的数据库对象定义导入到 Schema USERA 中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）。

**示例语句**：
```shell
# 导出 DDL 定义文件
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --ddl --all -f /output
	# 将 Schema USERA 中所有已支持的对象定义语句导出到 /output 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）。
```

> [!NOTE] `说明`：
> `--sys-user` 选项用于连接 sys 租户下拥有特定权限的用户。导入时如果未指定 `--sys-user` 选项时，默认指定的是 `--sys-user root`。

**任务结果示例**：
```shell
...
All Load Tasks Finished:

---------------------------------------------------------
|  No.#  | Type   | Name       | Count       | Status   | 
---------------------------------------------------------     
|  1     | TABLE  | table      | 1 -> 1      | SUCCESS  |                
---------------------------------------------------------

Total Count: 1          End Time: 2023-05-11 16:02:08
...
```


#### 2 导入 CSV 数据文件

#### 3 导入 SQL 数据文件

#### 4 导入 POS 数据文件

#### 5 导入 CUT 数据文件

#### 6 从 Amazon S3 导入数据文件到 OceanBase 数据库

#### 7 从 Aliyun OSS 导入数据文件到 OceanBase 数据库

#### 8 从 Apache Hadoop 导入数据文件到 OceanBase 数据库

#### 9 云数据库 OceanBase 导入模式和数据

#### 10 OceanBase 数据库导入模式和数据


### 参考文档
1. *obloader 快速入门*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775418)，；


