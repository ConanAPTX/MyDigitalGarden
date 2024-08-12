---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase 日志概述/","dgPassFrontmatter":true}
---



### OceanBase 日志概述
系统日志（SysLog）指 OceanBase 数据库程序日志，OceanBase 数据库在运行过程中自动打印的日志，用于监控报警和诊断；系统日志是监控指标的有力补充，两者共同支撑起 OceanBase 数据库的可观测能力；

系统日志与事务日志在有些场景下都简称为“日志”，但两者是完全不同的事物：
- 系统日志（SysLog）：OceanBase 数据库进程运行过程中打印的日志，用于监控报警和诊断，是可观测性领域的内容；
- 事务日志（commit log，简称 clog）：OceanBase 数据库先于数据持久化的 WAL，用于保证事务性，是数据一致性领域的内容；


#### 1 日志模块
程序日志分为不同的模块，且有父模块和子模块两个层级；

OceanBase 数据库的父模块包括 `CLIENT`、`CLOG`、`COMMON`、`ELECT`、`LIB`、`PROXY`、`RPC`、`RS`、`SERVER`、`SHARE`、`SQL`、`STORAGE`、`TLOG` 等；如果父模块下含有子模块，则表示为 `父模块.子模块`；

例如：`SQL.PARSER`，则 SQL 表示 SQL 模块，`SQL.PARSER` 表示 SQL 模块下的 PARSER 子模块；`SQL.*` 表示 SQL 模块下的所有子模块；


#### 2 日志文件
OceanBase 数据库日志模块所属的日志文件分为 `observer.log`、`election.log`、`rootservice.log`、`trace.log` 和 `alert.log` 五种类型，默认打印 INFO 级别以上的日志；

除 `trace.log`、`alert.log` 文件之外，每类日志文件自动生成一个带有 `.wf` 后缀的 WARNING 日志文件（`observer.log.wf`、`election.log.wf`、`rootservice.log.wf`），只打印 WARN 级别以上的日志。通过集群配置项 `enable_syslog_wf` 控制是否生成 WARNING 日志文件；

> [!NOTE] `注意`：
> V4.2.3 版本开始新增 Alert 日志，用于展示告警相关日志信息；

| 日志名称<img width=450/>| 日志路径<img width=450/>                                       | 备注                                                                                                                                                                                                      |
| -------------------------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 启动和运行日志：`observer.log`、`observer.log.wf`                    | OBServer 服务器的 `$work_dir/log` 目录下       | OceanBase 数据库所有的启动过程和启动后的运行过程中的日志                                                                                                                                                  |
| 选举日志：`election.log`、`election.log.wf`                          | OBServer 服务器的 `$work_dir/log` 目录下       | 选举模块记录的日志                                                                                                                                                                                        |
| Root Service 日志：<br>&emsp;`rootservice.log`、`rootservice.log.wf` | OBServer 服务器的 `$work_dir/log` 目录下       | RootService 模块记录的日志                                                                                                                                                                                |
| 全链路追踪日志：trace.log                                            | OBServer 服务器的 `$work_dir/log` 目录下       |                                                                                                                                                                                                           |
| 警告日志：alert.log                                                  | OBServer 服务器的 `$work_dir/log/alert` 目录下 |                                                                                                                                                                                                           |
| Clog                                                                 | OBServer 服务器的 `$work_dir/clog` 目录下      | Commit Log，所有Partition 共用，日志可能是乱序的，记录事务、Partition Service 提供的原始日志内容。此目录下的日志基于Paxos协议在多个副本之间同步（该Clog指代广义的Commit Log，代表整个事务的所有日志信息） |
| Ilog                                                                 | OBServer 服务器的 `$work_dir/ilog` 目录下      | Index Log，所有Partition 共用，单Partition 内部日志有序，记录Partition 内部log_id->clog(file_id, offset) 的索引信息；每个副本自行记录                                                                     |
| Slog                                                                 | OBServer 服务器的 `$work_dir/slog` 目录下      | 记录Storage Log，指SSTable操作日志信息                                                                                                                                                                    |


V4.2.3 版本开始新增 Alert 日志，用于展示告警相关日志信息；

OceanBase 数据库的单个日志文件大小不超过 256MB，当日志文件大小达到 256 MB 时，系统会进行日志轮转。原日志文件追加时间为后缀（格式为 `yyyyMMddHHmmss`），时间为本日志文件中最后一条日志的生成时间，并生成新的日志文件。发生日志轮转时，`.wf` 日志文件即使没到 256MB 也会一起轮转。也就是说 `xxx.log.wf` 文件和 `xxx.log` 文件总是一一对应的，`.wf` 文件一般情况下远小于 256 MB；

典型日志文件目录如下：
```log
log
├── election.log
├── election.log.wf
├── observer.log
├── observer.log.20220427154619
├── observer.log.wf
├── observer.log.wf.20220427154619
├── rootservice.log
├── rootservice.log.20220427165438
├── rootservice.log.wf
├── rootservice.log.wf.20220427165438
└── log
    ├── alert.log
    └── alert.log.20220304102928236
```

OceanBase 数据库原生支持日志归档（通过集群配置项 `enable_syslog_recycle` 控制），当日志总文件数达到 `max_syslog_file_count` 时，日志轮转时删除最老的日志文件。日志文件最大空间使用可按如下方式评估: `max_syslog_file_count * 256M * 3 * (enable_syslog_wf ? 2 : 1)`；

OceanBase 数据库原生日志归档功能基于文件数进行日志归档，当 OceanBase 数据库与外部管控系统（例如 OCP）一起部署时，通常使用外部管控系统提供的基于磁盘空间使用率的日志归档功能；

enable_syslog_recycle：是否开启回收系统日志的功能；
max_syslog_file_count：设置在回收日志文件之前可以容纳的日志文件数量；
syslog_io_bandwidth_limit：设置系统日志所能占用的磁盘 IO 带宽上限，超过带宽上限容量的系统日志将被丢弃；
enable_syslog_wf：设置是否把 WARN 以上级别的系统日志打印到一个单独的日志文件中；


#### 3 日志格式
典型的一行日志，及其格式如下：
```log
[2022-04-29 16:39:55.186527] WARN [COMMON] get_file_id_range (ob_log_file_group.cpp:127) [103594] [0]     [Y0-0000000000000000-0-0] [lt=17] [dc=0] 
	max file does not exist(max_file_id=4, b_exist=false)
```

日志格式分为两部分：【header】，【message】；如上格式所示，一条日志由头部（header）及消息体（message ）组成：

1. *header 部分*：
	1. `[2022-04-29 16:39:55.186527] WARN [COMMON] get_file_id_range (ob_log_file_group.cpp:127) [103594] [0] [Y0-0000000000000000-0-0] [lt=17] [dc=0]`
		1. time：`[2022-04-29 16:39:55.186527]`；该条日志打印的时间。
		2. log_level：`WARN`；该条日志的级别，目前支持以下级别（由高到低）：ERROR、USER_ERR、WARN、INFO、TRACE、DEBUG；
		3. module：`[COMMON]`；当前打印日志的模块名，由父模块和子模块组成，OceanBase 数据库支持按模块指定日志级别；
		4. function：`get_file_id_range`；当前打印日志的函数名；
		5. file_name:line_number：`(ob_log_file_group.cpp:127)`；当前打印日志的代码源文件名以及行号。
		6. thread_id：`[103594]`；当前打印日志的线程 ID。
		7. coroutine_id：`[0]`；当前打印日志的协程 ID。
		8. trace_id：`[Y0-0000000000000000-0-0]`：
			1. 用于跟踪一个任务的 trace_id，任务级唯一，该 trace_id 通过 RPC 在各个 OBServer 间传递，从而可以根据 trace_id 来获取一个任务的所有日志数据。
			2. 对应 `GV$OB_SQL_AUDIT` 视图中的 `TRACE_ID` 字段。
		9. log_used_time：`[lt=17]`：指上一条日志的处理时间 (异步日志场景包含日志写入文件时间)。
		10. dropped_msg_count：`[dc=0]`：dropped_msg_count：`dc=xx`，其中 `xx` 表示当前日志距离上一条日志期间丢弃的日志个数, 没有丢弃时为 0；
2. *message 部分*：消息体一般由消息描述信息（info）与参数（parameter）组成；
	1. `max file does not exist(max_file_id=4, b_exist=false)`
	    1. info：`max file does not exist`：具体的日志内容；
	    2. parameter：`(max_file_id=4, b_exist=false)`：
		    1. parameter：以 `name=value` 的 list 形式组成的 KV 列表。value 部分如果是简单类型直接展示，如果是复杂类型则展示为类似于 JSON 格式的文本（为提高可读性，去掉了 JSON 格式 KEY 上的 `"`）。



### 参考文档
1. *日志概述*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049904)，；


事务的日志包括：`redo log`, `prepare log`, `commit log`, `abort log`, `clear log` 等

Redo log 记录了事务的具体操作，比如某一行数据的某个字段从A修改为B；
Prepare log 记录了事务的 prepare 状态；
Commit log 表示这个事务成功 commit，并记录 commit 信息，比如事务的全局版本号；
Clear log 用于通知事务清理事务上下文；
Abort log 表示这个事务被回滚；

注意：所有的事务日志信息，不用于用户查看和定位系统问题