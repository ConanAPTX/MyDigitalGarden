---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase  日志压缩与解压/","dgPassFrontmatter":true}
---



### OceanBase 日志压缩与解压
在 V4.3.x 版本中，从 V4.3.1 版本开始支持系统日志压缩功能，可以达到 15~20 倍的压缩比；

通过压缩可以减小日志文件的大小，节省磁盘空间，并且方便传输和存储。解压缩则是将压缩的日志文件还原成原始的可阅读格式，以方便查看和分析日志内容；

#### 1 日志压缩
可以通过以下命令控制系统日志压缩功能；

1. 开启日志压缩功能。
    
    压缩功能开启时，`observer.log`、`rootservice.log`、`election.log`、`trace.log` 四种带有时间戳后缀的日志文件将会被压缩，对应的带有 `.wf` 后缀的文件则不会被压缩。 通过设置 `syslog_compress_func` 的算法来开启系统日志的压缩功能，可选择的算法有 `zstd_1.0` 算法和 `zstd_1.3.8` 算法。例如设置系统日志的压缩算法为 `zstd_1.3.8`。有关 `syslog_compress_func` 的详细信息请参见 [syslog_compress_func](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052433)。
    
    ```shell
    obclient> ALTER SYSTEM SET syslog_compress_func = 'zstd_1.3.8';
    ```
    
2. 设置系统日志磁盘空间；
当系统日志的总大小将要达到设置可用空间的上限时，最旧的日志文件将会被删除，包括已压缩的日志文件。 通过 `syslog_disk_size` 设置系统日志的磁盘空间上限。例如设置系统日志的磁盘空间为 100GB。有关 `syslog_disk_size` 的详细信息请参见 [syslog_disk_size](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052598)，；

> [!NOTE] `注意`：
> 具体磁盘空间大小以硬件规格和实际需求为准，如果想占满整个磁盘可以设置为 0GB；

```shell
obclient> ALTER SYSTEM SET syslog_disk_size='100GB';
```

3. 设置日志不压缩文件数量。 控制每种日志最少不压缩的文件数量，方便及时查看日志分析问题。实际不压缩的文件数可能超过该值，但不会小于该值；
通过 `syslog_file_uncompressed_count` 控制系统日志不压缩的文件数量。例如设置系统日志不压缩的文件数量为 50。有关 `syslog_file_uncompressed_count` 的详细信息请参见 [syslog_file_uncompressed_count](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052495)，；

> [!NOTE] `注意`：
> 具体数量以实际需求为准，例如将该值设置为 50，那么四种日志一共有 200 个不压缩的文件，每个 256MB，总大小约为 50GB；

```shell
obclient> ALTER SYSTEM SET syslog_file_uncompressed_count=50;
```


#### 2 日志解压
查看压缩后的日志文件有以下几种方式：

##### 2.1 使用 zstd 工具

1. 安装 zstd 工具；
从 zstd 官方网站[下载](https://github.com/facebook/zstd) zstd 工具；

2. 使用 zstd 工具解压；
```shell
# 使用 zstd 工具进行解压查看；
	1. 先使用 `zstd -d xxx.zst` 命令解压文件，其中 `xxx.zst` 指解压文件名称。
		
	2. 使用 `vim xxx` 命令查看文件内容，其中 `xxx` 指解压文件名称。
		
# 直接使用 zstd 工具查看压缩文件；
	使用 `zstd -dc xxx.zst` 命令直接查看压缩文件内容，其中 `xxx.zst` 指解压文件名称。
```


##### 2.2 使用 obdiag 工具
1. 安装 obdiag 工具；
有关 obdiag 工具的安装请参见 [obdiag 安装](https://www.oceanbase.com/docs/common-obdiag-cn-1000000000564045)，；
        
1. 使用 obdiag 工具解压
使用 `obdiag gather log [options]` 命令进行搜索压缩后的日志文件。有关 obdiag 工具的详细情况请参见 [obdiag gather log](https://www.oceanbase.com/docs/common-obdiag-cn-1000000000564056)，；


### 参考文档
1. *日志压缩与解压*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049897)，；


#### 1 日志监控项
可以通过 `GV$SYSSTAT` 视图可以方便的查看系统日志相关的监控项；
```sql
select CON_ID,CLASS,STAT_ID,NAME,VALUE,VALUE_TYPE 
from v$sysstat 
where class=128 and stat_id > 160000 and stat_id < 170000 and con_id=1; 
+--------+-------+---------+-----------------------------------------------+-----------+------------+
| CON_ID | CLASS | STAT_ID | NAME                                          | VALUE     | VALUE_TYPE |
+--------+-------+---------+-----------------------------------------------+-----------+------------+
|      1 |   128 |  160001 | oblogger log bytes                            | 205153873 | SET_VALUE  |
|      1 |   128 |  160002 | election log bytes                            |    482464 | SET_VALUE  |
|      1 |   128 |  160003 | rootservice log bytes                         | 222306504 | SET_VALUE  |
|      1 |   128 |  160004 | oblogger total log count                      |    418151 | SET_VALUE  |
|      1 |   128 |  160005 | election total log count                      |       521 | SET_VALUE  |
|      1 |   128 |  160006 | rootservice total log count                   |   1068461 | SET_VALUE  |
|      1 |   128 |  160007 | async tiny log write count                    |         0 | SET_VALUE  |
|      1 |   128 |  160008 | async normal log write count                  |         0 | SET_VALUE  |
|      1 |   128 |  160009 | async large log write count                   |         0 | SET_VALUE  |
|      1 |   128 |  160010 | async tiny log dropped count                  |         0 | SET_VALUE  |
|      1 |   128 |  160011 | async normal log dropped count                |         0 | SET_VALUE  |
|      1 |   128 |  160012 | async large log dropped count                 |         0 | SET_VALUE  |
|      1 |   128 |  160013 | async active tiny log count                   |         0 | SET_VALUE  |
|      1 |   128 |  160014 | async active normal log count                 |         0 | SET_VALUE  |
|      1 |   128 |  160015 | async active large log count                  |         0 | SET_VALUE  |
|      1 |   128 |  160016 | async released tiny log count                 |         0 | SET_VALUE  |
|      1 |   128 |  160017 | async released normal log count               |         0 | SET_VALUE  |
|      1 |   128 |  160018 | async released large log count                |         0 | SET_VALUE  |
|      1 |   128 |  160019 | async error log dropped count                 |         0 | SET_VALUE  |
|      1 |   128 |  160020 | async warn log dropped count                  |  65398852 | SET_VALUE  |
|      1 |   128 |  160021 | async info log dropped count                  |   3042983 | SET_VALUE  |
|      1 |   128 |  160022 | async trace log dropped count                 |      1163 | SET_VALUE  |
|      1 |   128 |  160023 | async debug log dropped count                 |         0 | SET_VALUE  |
|      1 |   128 |  160024 | async log flush speed                         |         0 | SET_VALUE  |
|      1 |   128 |  160025 | async generic log write count                 |   1475793 | SET_VALUE  |
|      1 |   128 |  160026 | async user request log write count            |         0 | SET_VALUE  |
|      1 |   128 |  160027 | async data maintain log write count           |         1 | SET_VALUE  |
|      1 |   128 |  160028 | async root service log write count            |         0 | SET_VALUE  |
|      1 |   128 |  160029 | async schema log write count                  |     11339 | SET_VALUE  |
|      1 |   128 |  160030 | async force allow log write count             |    138931 | SET_VALUE  |
|      1 |   128 |  160031 | async generic log dropped count               |  68422128 | SET_VALUE  |
|      1 |   128 |  160032 | async user request log dropped count          |         0 | SET_VALUE  |
|      1 |   128 |  160033 | async data maintain log dropped count         |         0 | SET_VALUE  |
|      1 |   128 |  160034 | async root service log dropped count          |         0 | SET_VALUE  |
|      1 |   128 |  160035 | async schema log dropped count                |     20870 | SET_VALUE  |
|      1 |   128 |  160036 | async force allow log dropped count           |         0 | SET_VALUE  |
|      1 |   128 |  160037 | async tiny log write count for error log      |         0 | SET_VALUE  |
|      1 |   128 |  160038 | async normal log write count for error log    |         0 | SET_VALUE  |
|      1 |   128 |  160039 | async large log write count for err/or log    |         0 | SET_VALUE  |
|      1 |   128 |  160040 | async tiny log dropped count for error log    |         0 | SET_VALUE  |
|      1 |   128 |  160041 | async normal log dropped count for error log  |         0 | SET_VALUE  |
|      1 |   128 |  160042 | async large log dropped count for error log   |         0 | SET_VALUE  |
|      1 |   128 |  160043 | async active tiny log count for error log     |         0 | SET_VALUE  |
|      1 |   128 |  160044 | async active normal log count for error log   |         0 | SET_VALUE  |
|      1 |   128 |  160045 | async active large log count for error log    |         0 | SET_VALUE  |
|      1 |   128 |  160046 | async released tiny log count for error log   |         0 | SET_VALUE  |
|      1 |   128 |  160047 | async released normal log count for error log |         0 | SET_VALUE  |
|      1 |   128 |  160048 | async released large log count for error log  |         0 | SET_VALUE  |
+--------+-------+---------+-----------------------------------------------+-----------+------------+
48 rows in set (0.05 sec)
```