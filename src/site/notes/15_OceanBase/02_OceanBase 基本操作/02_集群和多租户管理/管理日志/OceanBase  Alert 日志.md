---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase  Alert 日志/","dgPassFrontmatter":true}
---



### OceanBase Alert 日志
V4.2.3 版本开始新增 Alert 日志，用于展示告警相关日志信息；

#### 1 Alert 日志概述

Alert 日志是警告日志，包含了 OBServer 运行过程中重要的 INFO、WARN、ERROR 日志，旨在记录集群中的关键事件，为监控集群状态、排查并解决常见问题提供方便；


#### 2 Alert 日志格式
日志文件以 CSV 格式存储，每个日志字段间以 `|` 隔开，日志信息文本以 `""` 包裹；

`日志示例如下：`
```log
2023-12-21 13:46:57.650115|INFO|LOG|OB_LOG_SERVICE_START_SUCCESS|0|1001|51152|OBServer|Y5B690B7C0505-00060C87BB2B5971-0-0|start|ob_log_service.h:224|"OBServer log service start success, cost 638519 us."
```

Alert 日志包含以下 12 个字段：时间、级别、模块、事件、错误码、租户 ID、线程号、线程名、TRACE ID、函数名、代码位置、详细信息（包含解决方案）。 每个字段的具体含义如下：

| 字段       | 含义                             | 示例                                                    |
| -------- | ------------------------------ | ----------------------------------------------------- |
| 时间       | 日志打印的时间                        | 2023-12-21 13:46:57.650115                            |
| 级别       | 日志级别，如 INFO、WARN、ERROR         | INFO                                                  |
| 模块       | 日志所属的模块，如 STORAGE、SQL、TRANS    | STORAGE                                               |
| 事件       | 事件标识符，唯一标识了集群运行过程中发生的重要事件      | OB_LOG_SERVICE_START_SUCCESS                          |
| 错误码      | 错误对应的编码，INFO 日志的错误码为 0         | -4012                                                 |
| 租户ID     | 当前任务所属租户的编号                    | 1002                                                  |
| 线程号      | 当前线程的线程号                       | 51152                                                 |
| 线程名      | 当前线程的线程名                       | observer                                              |
| TRACE ID | 当前 SQL 语句的标识符，用于追踪一条 SQL 的执行过程 | Y5B690B7C0505-00060C87BB2B5971-0-0                    |
| 函数名      | 日志所处函数的函数名                     | start                                                 |
| 代码位置     | 日志所处的代码位置                      | ob_log_service.h:224                                  |
| 详细信息     | 日志事件描述、错误原因、解决方案               | “observer log service start success, cost 638519 us.” |

#### 3 Alert 日志存储

Alert 日志位于 `{observer 安装目录}/log/alert` 目录下，日志文件名为 `alert.log`。

单个 Alert 日志文件大小不超过 256MB，当日志文件大小达到 256 MB 时，系统会进行日志轮转。原日志文件追加时间为后缀（格式为 `yyyyMMddHHmmss`），例如 `alert.log.20220304102928236`。

#### 4 Alert 日志查询
##### 4.1 基于外表查看 Alert 日志
`alert.log` 文件以 CSV 格式存储，仅支持系统租户以外表的形式查看日志。

相比 Alert 日志文件，外表中增加了 IP 和 PORT 字段，表示 Alert 所属节点的 IP 地址和端口号。使用以下命令查看 Alert 日志：
```shell
obclient> SELECT * FROM sys_external_tbs.__all_external_alert_log_info;
```

查询结果如下：
```shell
+----------------------------+-------+----------------+---------------------------------------+---------+----------------+------+-----------+-----------+-------------+-----------------------------------+-------------------+--------------------------+-----------------------------------------------------------+
| time                       | level | module         | event                                 | errcode | ip             | port | tenant_id | thread_id | thread_name | trace_id                          | func_name         | code_location            | message                                                   |
+----------------------------+-------+----------------+---------------------------------------+---------+----------------+------+-----------+-----------+-------------+-----------------------------------+-------------------+--------------------------+-----------------------------------------------------------+
| 2024-04-01 14:01:21.367457 | INFO  | SERVER         | OB_SERVER_INIT_BEGIN                  |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | init              | ob_server.cpp:263        | [server_start 1/18] observer init begin.                  |
| 2024-04-01 14:01:21.378711 | INFO  | LIB            | OB_SERVER_SYSLOG_SERVICE_INIT_BEGIN   |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | init              | ob_log.cpp:1491          | [server_start 2/18] observer syslog service init begin.   |
| 2024-04-01 14:01:21.381666 | INFO  | LIB            | OB_SERVER_SYSLOG_SERVICE_INIT_SUCCESS |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | init              | ob_log.cpp:1565          | [server_start 3/18] observer syslog service init success. |
| 2024-04-01 14:01:22.267567 | INFO  | SERVER         | OB_SERVER_INIT_SUCCESS                |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | init              | ob_server.cpp:540        | [server_start 4/18] observer init success.                |
| 2024-04-01 14:01:22.267599 | INFO  | SERVER         | OB_SERVER_START_BEGIN                 |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | start             | ob_server.cpp:854        | [server_start 5/18] observer start begin.                 |
| 2024-04-01 14:01:22.267613 | INFO  | SERVER         | OB_SERVER_INSTANCE_START_BEGIN        |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | start             | ob_server.cpp:872        | [server_start 6/18] observer instance start begin.        |
| 2024-04-01 14:01:22.300143 | INFO  | STORAGE_BLKMGR | OB_SERVER_BLOCK_MANAGER_START_BEGIN   |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | start             | ob_block_manager.cpp:199 | [server_start 7/18] block manager start begin.            |
| 2024-04-01 14:01:22.354769 | INFO  | STORAGE_BLKMGR | OB_SERVER_BLOCK_MANAGER_START_SUCCESS |       0 | xx.xx.xx.xx    | 2882 |         0 |     70604 | observer    | Y0-00000000xxxxxxxx-0-0           | start             | ob_block_manager.cpp:256 | [server_start 8/18] block manager start success.          |
| 2024-04-01 14:02:15.592144 | INFO  | BOOTSTRAP      | OB_BOOTSTRAP_PREPARE_BEGIN            |       0 | xx.xx.xx.xx    | 2882 |         1 |     71165 | T1_L0_G0    | YB42AC1E87E2-00061502xxxxxxxx-0-0 | prepare_bootstrap | ob_bootstrap.cpp:243     | bootstrap prepare begin.                                  |
| 2024-04-01 14:02:21.341029 | INFO  | BOOTSTRAP      | OB_BOOTSTRAP_PREPARE_SUCCESS          |       0 | xx.xx.xx.xx    | 2882 |         1 |     71165 | T1_L0_G0    | YB42AC1E87E2-00061502xxxxxxxx-0-0 | prepare_bootstrap | ob_bootstrap.cpp:275     | bootstrap prepare success.                                |
+----------------------------+-------+----------------+---------------------------------------+---------+----------------+------+-----------+-----------+-------------+-----------------------------------+-------------------+--------------------------+-----------------------------------------------------------+
10 rows in set, 6 warnings (0.008 sec)
```

如果 `alert.log` 文件达到了 256MB，会有新的 Alert 日志文件生成，此时需要手动刷新外表的文件列表：
```shell
obclient> ALTER EXTERNAL TABLE sys_external_tbs.__all_external_alert_log_info refresh;
```

刷新后再进行查询，即可查到所有日志内容。


> [!NOTE] `注意`：
> 请不要手动创建 `__all_external_alert_log_info` 表，以确保与工具的兼容性和升级的一致性。

##### 4.2 基于 Alert 日志排查问题
###### 4.2.1 INFO 日志
通过 `alert.log` 文件中的 INFO 日志，可以查看某个流程的进度。例如下面这条 OBServer 启动场景的日志，通过 `[server_start 3/12]` 信息可以知道 observer 进程启动流程包含 12 个步骤，目前进行到了第三步，这一步的事件标识符是 `OB_SERVER_SYSLOG_SERVICE_INIT_SUCCESS`，表示“系统日志服务初始化成功”；

```log
2024-02-22 11:39:39.910566|INFO|SERVER|OB_SERVER_START_BEGIN|0|0|88288|OBServer|Y0-00000000xxxxxxxx-0-0|main|main.cpp:556|"[server_start 1/12] OBServer start begin."
2024-02-22 11:39:39.933725|INFO|LIB|OB_SERVER_SYSLOG_SERVICE_INIT_BEGIN|0|0|88288|OBServer|Y0-00000000xxxxxxxx-0-0|init|ob_log.cpp:1457|"[server_start 2/12] OBServer syslog service start begin."
2024-02-22 11:39:39.937152|INFO|LIB|OB_SERVER_SYSLOG_SERVICE_INIT_SUCCESS|0|0|88288|OBServer|Y0-00000000xxxxxxxx-0-0|init|ob_log.cpp:1531|"[server_start 3/12] OBServer syslog service start success."
```

###### 4.2.2 WARN/ERROR 日志
通过 `alert.log` 文件中的 WARN日志和 ERROR 日志可以查看事件的描述信息，还可以获取错误原因和解决方式。例如下面这条显示 CLOG 模块对磁盘进行 Resize 操作失败的日志，给出的错误原因是新的磁盘大小不足以容纳所有租户，同时也给出了解决方式是将 `log_disk_size` 设置为一个大于 43526MB 的值，从而解决这一问题；

```log
2023-11-17 03:19:38.592090|ERROR|CLOG|OB_CLOG_ALLOCATE_DISK_SPACE_FAILED|-4290|0|2569344|OBServer|Y0-0000000000000000-0-0|resize_|ob_server_log_block_mgr.cpp:211|"new log_disk_size(21504MB) is not enough to hold all tenants. [suggestion] set log_disk_size greater than 43526MB."
```


### 参考文档
1. *Alert 日志*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049902)，；


