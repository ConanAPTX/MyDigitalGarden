---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/导数工具：obloader，obdumper/ob-loader-dumper 使用教程/","dgPassFrontmatter":true}
---


### ob-loader-dumper 使用教程

#### 1 obloader，导入数据

> [!NOTE] `注意事项`：
> 1. 若需要导入文件太大，强烈建议对文件进行分隔，请参考：[准备好您的数据了吗？](https://open.oceanbase.com/blog/1100272?_gl=1*1akqo6q*_ga*MTMwNTcwODg5Ny4xNzE5NTU4MjE0*_ga_T35KTM57DZ*MTcyMDA4MDMzOS4xNy4xLjE3MjAwODQ3MjcuNTkuMC4w)，；

```shell
# 进入运行脚本所在的目录 
cd {ob-loader-dumper}/bin  
./obloader -h 'IP地址' -P'端口' -u'user@tenant#cluster' -p'密码' -D'库名' --table '表名' --csv -f '文件路径' --sys-password '系统租户密码' --external
```

```shell
<<xxxx
obloader，obdumper 参数介绍：
	-h(--host)：连接 ODP 或者 OceanBase 物理节点的主机地址。      
	-P(--port)：连接 ODP 或者 OceanBase 物理节点的主机端口。      
	-u(--user)：指定数据库用户名、集群的租户名、集群名。 格式：<user>@<tenant>#<cluster>；
		用于连接目标数据库的用户名、租户名和集群名。格式：<user>@<tenant>#<cluster>，示例：-u user@tenant#cluster；
	-p(--password)：数据库密码。
	-D(--database)：数据库名；说明：OBDUMPER 每次仅支持导出一个数据库的数据；
	--all：导出所有已支持的数据库对象定义和表数据；
	--table：导出 表 定义或者 表 数据；'*' 表示导入(导出)所有表；
	--ddl：导入(导出) DDL 文件； 
		DDL 文件是指导出的数据库对象定义文件，文件的命名规范为 对象名-schema.sql；命令行指定该选项时，表示仅导入(导出)数据库对象定义，不会导入(导出)数据；
		导出多个表对象定义时，建议 --thread 选项的参数值不超过 4。并发数太大会影响访问 sys 租户下的内部视图，导出时会出现超时错误；
			注意：文件中避免出现注释或者开关语句等。如果数据库对象之间存在依赖，可能会导入失败，需要人工介入处理；
	--csv：导出 CSV 格式的数据文件；【推荐】
		CSV 文件是指数据按照标准的 CSV 格式存储，文件的命名规范为 表名.csv ；有关 CSV 格式规范，请参考 RFC 4180 中的定义。
		CSV 格式最容易出错的是定界符，通常使用的定界符是单引号或者双引号。如果数据中存在定界符，必须做转义处理，即指定转义符，请参考 CSV 格式相关的符号选项。
		强烈建议使用 CSV 格式。建议该选项搭配 --table 选项使用。如果搭配 --all 选项时，OBDUMPER 只会导出表中的数据，不会导出数据库对象定义。
	-f 'file_path', --file-path= 'file_path'：用于指定数据文件存放在本地磁盘的绝对路径； 
	--sys-user：指定 sys 租户下用户名；用于连接 sys 租户下拥有特定权限的用户。导入时如果未指定--sys-user 选项时，默认指定的是 --sys-user root；      
	--sys-password：指定 sys 租户下特定用户的密码；
	--rw float_num：用于标识文件解析线程占总线程数的比例。默认值：1；
		该选项与 `--thread` 选项搭配使用，可计算出文件解析线程数等于 `--thread` 选项值 * `--rw` 选项值；
	--thread int_num：用于标识并发线程数。该选项直接对应写入线程数；
		与 --rw 选项搭配使用时，用于计算文件解析线程数，计算公式：--thread 参数值 * --rw 参数值。默认值：CPU 乘以 2；
		OceanBase 是串行执行 DDL，所以导入数据库对象定义时无需指定该选项；
xxxx
```
{ #08fda9}


```shell
<<xxxx
# obloader 参数介绍：   
xxxx
```


关于 *obloader* 命令选项可以在【参考文档】中查看：*obloader 命令行选项*；

```shell
# 使用实例：
## 导入 DDL 定义文件
./obloader -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --ddl --all -f /output
	# 将 /output 目录下所有已支持的数据库对象定义导入到 Schema USERA 中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）
## 导入 CSV 数据文件
./obloader -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --csv --table '*' -f /output
	# 将 /output 目录下所有已支持的 CSV 数据文件导入到 Schema USERA 中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）
## 导入 SQL 数据文件
./obloader -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --sql --table '*' -f /output
	# 将 `/output` 目录下所有已支持的 SQL 数据文件导入到 Schema USERA 中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）；
	# SQL 数据文件（后缀名 .sql）存储的是 Insert SQL 语句，可通过文本编辑器或者 SQL 编辑器等工具直接打开；
```

`obloader` 命令还支持：*导入 POS 数据文件，导入 CUT 数据文件，从 Amazon S3 导入数据文件到 OceanBase 数据库，从 Aliyun OSS 导入数据文件到 OceanBase 数据库，从 Apache Hadoop 导入数据文件到 OceanBase 数据库，云数据库 OceanBase 导入模式和数据，OceanBase 数据库导入模式和数据*；

更多详情请参考：[[15_OceanBase/06_OceanBase 数据迁移/导数工具：obloader，obdumper/obloader  命令使用示例\|obloader  命令使用示例]]，*obloader 快速入门*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775418)，；

#### 2 obdumper，导出数据
```shell
# 2 进入运行脚本所在的目录 
cd {ob-loader-dumper}/bin 
./obdumper -h 'IP地址' -P'端口' -u'user@tenant#cluster' -p'密码' -D'库名' --table '表名' --csv -f '文件路径' --sys-password '系统租户密码' --skip-check-dir
```


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/06-ocean-base/obloader-obdumper/ob-loader-dumper/#08fda9" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



```shell
<<xxxx
obloader，obdumper 参数介绍：
	-h(--host)：连接 ODP 或者 OceanBase 物理节点的主机地址。      
	-P(--port)：连接 ODP 或者 OceanBase 物理节点的主机端口。      
	-u(--user)：指定数据库用户名、集群的租户名、集群名。 格式：<user>@<tenant>#<cluster>；
		用于连接目标数据库的用户名、租户名和集群名。格式：<user>@<tenant>#<cluster>，示例：-u user@tenant#cluster；
	-p(--password)：数据库密码。
	-D(--database)：数据库名；说明：OBDUMPER 每次仅支持导出一个数据库的数据；
	--all：导出所有已支持的数据库对象定义和表数据；
	--table：导出 表 定义或者 表 数据；'*' 表示导入(导出)所有表；
	--ddl：导入(导出) DDL 文件； 
		DDL 文件是指导出的数据库对象定义文件，文件的命名规范为 对象名-schema.sql；命令行指定该选项时，表示仅导入(导出)数据库对象定义，不会导入(导出)数据；
		导出多个表对象定义时，建议 --thread 选项的参数值不超过 4。并发数太大会影响访问 sys 租户下的内部视图，导出时会出现超时错误；
			注意：文件中避免出现注释或者开关语句等。如果数据库对象之间存在依赖，可能会导入失败，需要人工介入处理；
	--csv：导出 CSV 格式的数据文件；【推荐】
		CSV 文件是指数据按照标准的 CSV 格式存储，文件的命名规范为 表名.csv ；有关 CSV 格式规范，请参考 RFC 4180 中的定义。
		CSV 格式最容易出错的是定界符，通常使用的定界符是单引号或者双引号。如果数据中存在定界符，必须做转义处理，即指定转义符，请参考 CSV 格式相关的符号选项。
		强烈建议使用 CSV 格式。建议该选项搭配 --table 选项使用。如果搭配 --all 选项时，OBDUMPER 只会导出表中的数据，不会导出数据库对象定义。
	-f 'file_path', --file-path= 'file_path'：用于指定数据文件存放在本地磁盘的绝对路径； 
	--sys-user：指定 sys 租户下用户名；用于连接 sys 租户下拥有特定权限的用户。导入时如果未指定--sys-user 选项时，默认指定的是 --sys-user root；      
	--sys-password：指定 sys 租户下特定用户的密码；
	--rw float_num：用于标识文件解析线程占总线程数的比例。默认值：1；
		该选项与 `--thread` 选项搭配使用，可计算出文件解析线程数等于 `--thread` 选项值 * `--rw` 选项值；
	--thread int_num：用于标识并发线程数。该选项直接对应写入线程数；
		与 --rw 选项搭配使用时，用于计算文件解析线程数，计算公式：--thread 参数值 * --rw 参数值。默认值：CPU 乘以 2；
		OceanBase 是串行执行 DDL，所以导入数据库对象定义时无需指定该选项；
xxxx
```

</div></div>

```shell
<<xxxx
# obdumper 参数介绍：   
xxxx
```


关于 *obdumper* 命令选项可以在【参考文档】中查看：*obdumper 命令行选项*；

```shell
# 使用实例：
## 导出 DDL 定义文件
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --ddl --all -f /output
	# 将 Schema USERA 中所有已支持的对象定义语句导出到 /output 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）。
## 导出 CSV 数据文件
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --csv --table '*' -f /output 
	# 将 Schema USERA 中所有表中的数据按照 CSV 格式导出到 `/output` 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）
## 导出 SQL 数据文件
./obdumper -h xx.x.x.x -P 2883 -u test@mysql#cluster_a -p ****** --sys-user **u*** --sys-password ****** -D USERA --sql --table '*' -f /output
	# 将 Schema USERA 中所有表中的数据按照 SQL 格式导出到 `/output` 目录中（OceanBase 4.0.0 之前的版本要求提供 sys 租户的密码）
```

`obdumper` 命令还支持：*导出 CUT 数据文件，导出数据文件到 Amazon S3，导出数据文件到 Aliyun OSS，自定义导出文件名，导出数据时使用控制文件，导出表中指定列的数据，导出自定义查询结果集，云数据库 OceanBase 导出模式和数据，OceanBase 数据库导出模式和数据*；

更多详情请参考：[[15_OceanBase/06_OceanBase 数据迁移/导数工具：obloader，obdumper/obdumper 命令使用示例\|obdumper 命令使用示例]]，*obdumper 快速入门*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775409)，；


### 参考文档
1. *obloader 命令行选项*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775421)，；
2. *obloader 快速入门*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775418)，；
3. *obdumper 命令行选项*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775406)，；



