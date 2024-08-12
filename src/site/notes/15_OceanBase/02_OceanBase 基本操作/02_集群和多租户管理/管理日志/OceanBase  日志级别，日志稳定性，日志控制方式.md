---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理日志/OceanBase  日志级别，日志稳定性，日志控制方式/","dgPassFrontmatter":true}
---


### OceanBase 日志级别，日志稳定性，日志控制方式

#### 1 日志级别
*日志级别* 即 *日志打印级别*；OceanBase 数据库通过设置日志模块的日志打印级别来控制写入日志文件的日志数据；  
  
*observer.log*、*observer.log.wf*、*election.log election.log.wf*、*rootservice.log* 和 *rootservice.log.wf* 日志文件的默认打印级别均为 WDIAG 级别，即只有 WDIAG 及以上级别的日志数据才会打印到该日志文件中；  

OceanBase 数据库日志划分了七个日志级别，含义如下表所示；表中的日志级别从高到低依次排列；

|日志级别|含义|
|---|---|
|ERROR|严重错误。用于记录系统的故障信息，且必须进行故障排除，否则系统不可用。|
|WARN|警告。系统能继续提供服务，但行为可能不符合预期，或可能将有严重错误发生，需进行故障排除。|
|INFO|提示。用于记录系统运行的当前状态，该信息为正常信息。|
|EDIAG|Error Diagnosis, 协助故障排查的诊断信息，通常为 OceanBase 数据库程序的 Bug。|
|WDIAG|Warning Diagnosis, 协助故障排查的诊断信息，预期内的错误，OceanBase 数据库能进行容错。|
|TRACE|SQL 语句级调试信息，日志数与 SQL 复杂度相关与访问数据量无关。|
|DEBUG|调试信息。用于调试时更详细地了解系统运行状态，包括当前调用的函数名、参数、变量、函数调用返回值等。|
本文介绍的查看和设置日志级别仅对普通日志文件（`election.log`、`observer.log`、`rootservice.log`）生效；对于 WARNING 日志文件（带有 `.wf` 后缀的日志文件），总是打印 WARN 及高于 WARN 日志级别的日志；



##### 1.1 查询，设置日志打印级别
###### 1.1.1 查看当前的日志打印级别
可通过 `SHOW PARAMETERS` 命令来查看当前的日志打印级别，*日志的默认打印级别为 WDIAG 级别*；

```sql
-- 查看当前的日志打印级别  
obclient> SHOW PARAMETERS LIKE '%syslog_level%';
+-------+----------+----------------+----------+--------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------+----------+---------+---------+-------------------+---------------+-----------+
| zone  | svr_type | svr_ip         | svr_port | name         | data_type | value | info                                                                                                                         | section  | scope   | source  | edit_level        | default_value | isdefault |
+-------+----------+----------------+----------+--------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------+----------+---------+---------+-------------------+---------------+-----------+
| zone1 | observer | 172.xx.xxx.xxx |     2882 | syslog_level | NULL      | WDIAG | specifies the current level of logging. There are DEBUG, TRACE, WDIAG, EDIAG, INFO, WARN, ERROR, seven different log levels. | OBSERVER | CLUSTER | DEFAULT | DYNAMIC_EFFECTIVE | WDIAG         |         1 |
+-------+----------+----------------+----------+--------------+-----------+-------+------------------------------------------------------------------------------------------------------------------------------+----------+---------+---------+-------------------+---------------+-----------+
1 row in set

```


###### 1.1.2 设置日志打印级别
OceanBase 数据库支持设置日志级别，从而获取更精确的目标日志；可以从三个级别设置：【`系统级别`】、【`Session 级别`】、【`语句级别`】，打印日志时，优先级从高到低分别为语句、Session、系统；

1. OceanBase 数据库也支持按模块设置：
	1. *系统级别*：作用范围为整个集群所有 OBServer；仅支持在系统租户下配置；
	2. *Session 级别*：作用范围为当前租户在集群内所在的 OBServer；
		1. 设置 Session 级别的变量仅对当前 Session 有效，对其他 Session 无效；
		2. 设置 Global 级别的变量对当前 Session 无效，需要重新登录建立新的 Session 才会生效；
	3. *语句级别*：作用范围为当前执行语句所在的 OBServer；只在 SQL 语句执行期间生效；

以下为通过【系统级别】，【Session 级别】，【语句级别】三个级别对日志打印级别进行设置的具体方法；

1. *通过系统级别设置日志模块的级别*
可通过如下两种方式进行设置；

通过系统配置项 `syslog_level` 设置系统级别的日志级别。例如：设置 SQL 模块的日志级别为 `debug`，设置 COMMON 模块的日志级别为 `error`；    
```shell
-- 1.通过系统级别设置日志模块的级别，通过系统配置项 syslog_level 设置系统级别的日志级别；  
-- 设置 SQL 模块的日志级别为 debug，设置 COMMON 模块的日志级别为 error；  
obclient > ALTER SYSTEM SET syslog_level='sql.*:debug, common.*:error';
obclient > ALTER SYSTEM SET syslog_level='sql.*:debug, common.*:error';
```

通过操作系统 `bash kill -41/42 $pid` 命令设置系统级别的日志级别；

`kill -41` 用于降低程序日志的级别。例如：当前程序日志的级别为 INFO，执行 `kill -41` 后，则级别降低至 TRACE。`kill -42` 用于升高程序日志的级别。例如：当前程序日志的级别为 INFO，执行 `kill -42` 后，则级别升高至 WARN；

推荐通过系统配置项 `syslog_level` 设置系统级别的日志级别。因为 OceanBase 数据库在刷新配置时，会导入 `config` 文件（OceanBase 安装目录下的 `etc/observer.config.bin`）中配置的 `syslog_level`，导致 kill 方式失效；

> [!NOTE] `注意`：
> 建议仅在 SQL 客户端登录不了 OceanBase 集群的情况下使用 `kill -41/kill -42` 调节日志级别；


2. *通过 Session 级别设置日志模块的级别*

通过系统变量 `ob_log_level` 设置 Session 级别的日志级别。例如：设置 SQL 模块的日志级别为 `debug`，设置 COMMON 模块的日志级别为 `info`；
```sql
-- 2.通过 Session 级别设置日志模块的级别，通过系统变量 ob_log_level 设置 Session 级别的日志级别；  
-- 设置 SQL 模块的日志级别为 debug，设置 COMMON 模块的日志级别为 info  
obclient > SET @@ob_log_level='sql.*:debug, common.*:info';  
obclient > SET @@ob_log_level='sql.*:debug, common.*:info';
```


3. *通过语句级别设置日志模块的级别*

可以通过 Hint 设置语句级别的日志级别。例如：设置 SQL 模块的日志级别为 `debug`，设置 COMMON 模块的日志级别为 `info`。更多 Hint 语句相关的介绍，请参见 [Optimizer Hint](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001053019)，；
```sql
-- 3.通过语句级别设置日志模块的级别；可以通过 Hint 设置语句级别的日志级别；
-- 设置 SQL 模块的日志级别为 debug，设置 COMMON 模块的日志级别为 info  
obclient > SELECT /*+log_level('sql.*:debug, common.*:info')*/ * FROM t;
obclient > SELECT /*+log_level('sql.*:debug, common.*:info')*/ * FROM t;
```

> [!NOTE] 说明：
> 如果使用 MySQL 的 C 客户端执行带 Hint 的 SQL 语句，需要使用 `-c` 选项登录，否则 MySQL 客户端会将 Hint 作为注释从用户 SQL 语句中去除，导致系统无法收到用户 Hint；

#### 2 日志稳定性
同步打印日志的方案，指工作线程同时负责日志内容封装和写文件操作，除了对程序性能的损耗，某些 IO 异常可能导致写日志阻塞进而 SQL RT 抖动，甚至造成 OceanBase 数据库进程状态异常。OceanBase 数据库引入了异步打印日志的解决方案。工作线程仅负责日志内容的封装, 写文件操作交给单独线程负责，从而提升程序性能和系统稳定性。通过集群配置项 `enable_async_syslog` 控制是否开启异步日志打印功能，默认开启；

> [!NOTE] 注意：
> 出于稳定性考虑建议开启异步日志。但是开启异步日志后，在 OBServer 异常退出的情况下，会有最后一小段日志没来得及刷盘而丢失，所以在测试环境中建议关闭异步日志；

系统异常情况下，OceanBase 数据库会打印大量日志。为了保留更长时间的日志以便排查问题，OceanBase 数据库支持日志限流功能。通过集群配置项 `syslog_io_bandwidth_limit` 控制日志打印速率，默认值为 30MB/秒。发生日志限流时会随机丢弃日志，同时打印 `REACH SYSLOG RATE LIMIT` 的 INFO 日志，ERROR 级别的日志不会被限流；

> [!NOTE] 注意：
> 虽然有异步日志打印方案，但依然会有 CPU 损耗。某些极致性能场景下（例如大促活动）可以将日志级别设置为 ERROR 以减少日志量；



### 参考文档
1. *日志级别*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049901)，；
2. *日志稳定性*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049903)，；
3. *日志控制方式*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001049898)，；


