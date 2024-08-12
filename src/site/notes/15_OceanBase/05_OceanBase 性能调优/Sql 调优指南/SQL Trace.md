---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/SQL Trace/","dgPassFrontmatter":true}
---


### SQL Trace

*SQL Trace* 能够交互式的提供上一次执行的 SQL 请求执行过程中调用链路情况，以及链路中各阶段耗时情况，以便进行性能分析或调优，可以使用 `SHOW TRACE` 功能便捷地找到性能瓶颈点；

在连接 OceanBase 数据库 V4.0.0 及之前版本的数据库时，请继续使用 `ob_enable_trace_log` 变量控制 Show Trace 功能的开启和关闭；

OceanBase 数据库 V4.1.0 版本新增系统变量 `ob_enable_show_trace` 用于控制全链路诊断 Show Trace 功能的开启关闭；

#### 1 V2.x，3.x，4.0.0 版本
##### 1.1 开启，关闭 SQL Trace

*SQL Trace* 功能默认是关闭的，可通过 `session` 变量来控制其打开和关闭；

```sql
-- 登录业务租户
obclient -h10.x.x.x -P2883 -uroot@mytenant#obcluster -p

-- 查询变量
SHOW VARIABLES LIKE '%ob_enable_trace_log%';

-- 打开 / 关闭 SQL Trace 功能
obclient> SET ob_enable_trace_log = 1;   -- 打开 SQL Trace 功能
obclient> SET ob_enable_trace_log = 0;   -- 关闭 SQL Trace 功能

/* -- 未验证，需谨慎使用
-- 设置 GLOBAL 级别变量，重新连接或连接生效；
SET GLOBAL ob_enable_trace_log = ON;    -- 开启
SET GLOBAL ob_enable_trace_log = OFF;   -- 关闭
*/

-- 
SHOW TRACE;
```


##### 1.2 使用 `SHOW TRACE`
使用 `SHOW TRACE` 查看某一 SQL 的执行信息；
```sql
-- 1. 打开 SQL Trace
	SET ob_enable_trace_log = 1;
	Query OK, 0 rows affected

-- 2. 执行目标 SQL
	CREATE TABLE t1(c1 INT,c2 INT,c3 INT);
	INSERT INTO t1 VALUES(1,1,1);
	INSERT INTO t1 VALUES(2,2,2);
	SELECT * FROM t1 WHERE c1 = 1;
	+----+------+------+
	| c1 | c2   | c3   |
	+----+------+------+
	|  1 |    1 |    1 |
	+----+------+------+

-- 3. 显示 Trace
SHOW TRACE;
+------------------------------+---------------------------------------------------------------------------------------------------+
| TITLE                        | KEYVALUE                                                                   | TIME |
+------------------------------+----------------------------------------------------------------------------+------+
| process begin                | in_queue_time:17, receive_ts:1612420489580707, enqueue_ts:1612420489580709 | 0    |
| query begin                  | trace_id:YB42AC1E87E6-0005B8AB2D57844F                                     | 1    |
| parse begin                  | stmt:"set ob_enable_trace_log = 1", stmt_len:27                            | 62   |
| pc get plan begin            | NULL                                                                       | 9    |
| pc get plan end              | NULL                                                                       | 19   |
| transform_with_outline begin | NULL                                                                       | 1    |
| transform_with_outline end   | NULL                                                                       | 31   |
| resolve begin                | NULL                                                                       | 21   |
| resolve end                  | NULL                                                                       | 33   |
| execution begin              | arg1:false, end_trans_cb:false                                             | 14   |
| start_auto_end_plan begin    | NULL                                                                       | 39   |
| start_auto_end_plan end      | NULL                                                                       | 1    |
| execution end                | NULL                                                                       | 11   |
| query end                    | NULL                                                                       | 39   | 
+------------------------------+---------------------------------------------------------------------------------------------+------+
/* 字段介绍：
	Title：记录执行过程某一个阶段点；
		Title 列包含整个 SQL 执行经历的各个阶段的信息以及该 SQL 真实的执行路径。
		上述示例的结果中有经过 Resolve、Transform、Optimizer 和 Code Generate 四个流程，说明该 SQL 重新生成了计划，没有命中 Plan Cache；
	KeyValue：记录某一个阶段点产生的一些执行信息；
		KeyValue 列包含一些执行信息，可以用于排查问题：
		    trace_id 可以作为 sql_audit 中的过滤条件，快速找到该 SQL 执行信息，同时也可以通过该 trace_id 快速查找相关的 OBServer 日志。
		    plan_id 可以用于在 v$plan_cache_plan_explain 中查看 Plan Cache 中缓存的具体执行计划。
		    phy_plan_type 指出该次执行计划的类型（1 表示本地计划/2 表示远程计划/3 表示分布式计划），可以辅助 SQL 诊断；
	Time：记录上一个阶段点到这次阶段点执行耗时（us）；
		Time 列显示上一个阶段点到这次阶段点执行耗时；
		如果某个 SQL 执行很慢，则通过查看 time 列，能够快速定位出具体是哪个阶段执行较慢，然后再进行具体分析。此例中，执行耗时主要在生成计划过程中，
		因此只需要分析没有命中 Plan Cache 的原因，可能是计划淘汰后第一次执行该 SQL，或是 Plan Cache 不支持的 SQL；
*/
```


#### 2 V4.1.0 及以后版
在连接 OceanBase 数据库 V4.0.0 及之前版本的数据库时，请继续使用 `ob_enable_trace_log` 变量控制 Show Trace 功能的开启和关闭；

OceanBase 数据库 V4.1.0 版本新增系统变量 `ob_enable_show_trace` 用于控制全链路诊断 Show Trace 功能的开启关闭；

##### 2.1 开启，关闭 SQL Trace
```sql
-- 查询变量 
SHOW VARIABLES LIKE '%ob_enable_show_trace%';

-- 打开 / 关闭 SQL Trace 功能
obclient> SET ob_enable_show_trace = 1;   -- 打开 SQL Trace 功能
obclient> SET ob_enable_show_trace = 0;   -- 关闭 SQL Trace 功能
```

##### 2.2 使用 `SHOW TRACE` 
使用 `SHOW TRACE` 查看某一 SQL 的执行信息；
```sql
-- 1. 打开 SQL Trace
SET ob_enable_show_trace = 1;   
Query OK, 0 rows affected

-- 2. 执行目标 SQL
	CREATE TABLE t1(c1 INT,c2 INT,c3 INT);
	INSERT INTO t1 VALUES(1,1,1);
	INSERT INTO t1 VALUES(2,2,2);
	
	SELECT/*+PARALLEL(2)*/ COUNT(*) FROM t1;
	+----------+
	| COUNT(*) |
	+----------+
	|        2 |
	+----------+
	1 row in set

-- 3. 显示 Trace
SHOW TRACE;
+-------------------------------------------------------+---------------------+-------------+
| OPERATION                                             | START_TIME          | ELAPSE_TIME |
+-------------------------------------------------------+---------------------+-------------+
| com_query_process                                     | 2023/03/07 16:27:43 | 5241 µs     |
| └── mpquery_single_stmt                               | 2023/03/07 16:27:43 | 5208 µs     |
|     ├── sql_compile                                   | 2023/03/07 16:27:43 | 4149 µs     |
|     │   ├── pc_get_plan                               | 2023/03/07 16:27:43 | 108 µs      |
|     │   └── hard_parse                                | 2023/03/07 16:27:43 | 3952 µs     |
|     │       ├── parse                                 | 2023/03/07 16:27:43 | 97 µs       |
|     │       ├── resolve                               | 2023/03/07 16:27:43 | 492 µs      |
|     │       ├── rewrite                               | 2023/03/07 16:27:43 | 598 µs      |
|     │       ├── optimize                              | 2023/03/07 16:27:43 | 2022 µs     |
|     │       │   ├── inner_execute_read                | 2023/03/07 16:27:43 | 485 µs      |
|     │       │   │   ├── sql_compile                   | 2023/03/07 16:27:43 | 140 µs      |
|     │       │   │   │   └── pc_get_plan               | 2023/03/07 16:27:43 | 81 µs       |
|     │       │   │   ├── open                          | 2023/03/07 16:27:43 | 43 µs       |
|     │       │   │   ├── get_das_id                    | 2023/03/07 16:27:43 | 17 µs       |
|     │       │   │   └── do_local_das_task             | 2023/03/07 16:27:43 | 107 µs      |
|     │       │   └── close                             | 2023/03/07 16:27:43 | 66 µs       |
|     │       │       ├── close_das_task                | 2023/03/07 16:27:43 | 17 µs       |
|     │       │       └── end_transaction               | 2023/03/07 16:27:43 | 6 µs        |
|     │       ├── code_generate                         | 2023/03/07 16:27:43 | 197 µs      |
|     │       └── pc_add_plan                           | 2023/03/07 16:27:43 | 74 µs       |
|     └── sql_execute                                   | 2023/03/07 16:27:43 | 979 µs      |
|         ├── open                                      | 2023/03/07 16:27:43 | 19 µs       |
|         ├── response_result                           | 2023/03/07 16:27:43 | 768 µs      |
|         │   ├── get_das_id                            | 2023/03/07 16:27:43 | 0 µs        |
|         │   └── do_local_das_task                     | 2023/03/07 16:27:43 | 358 µs      |
|         └── close                                     | 2023/03/07 16:27:43 | 145 µs      |
|             └── close_das_task                        | 2023/03/07 16:27:43 | 19 µs       |
+-------------------------------------------------------+---------------------+-------------+
27 rows in set
```


### 参考文档
1. *SQL Trace*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945962)，[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220959)，；
2. [全链路追踪 Show Trace](https://www.oceanbase.com/docs/common-oceanbase-connector-j-cn-1000000000845357)，；




###  废弃备份

设置变量 *ob_enable_trace_log* / *ob_enable_plan_cache*；

> [!NOTE] `注意`：
> 建议开启 plan cache 之前，先开启 SQL Audit；

#### 1 设置 Session 级别变量
设置 Session 级别变量，仅当前连接生效，重新连接需要重新设置；

```sql
-- 登录业务租户
obclient -h10.x.x.x -P2883 -uroot@mytenant#obcluster -p

-- 查询变量
SHOW VARIABLES LIKE '%ob_enable_trace_log%';
SHOW VARIABLES LIKE '%ob_enable_plan_cache%';

-- 开启
SET ob_enable_trace_log = ON;
SET ob_enable_plan_cache = ON;

-- 关闭
SET ob_enable_trace_log = OFF;
SET ob_enable_plan_cache = OFF;
```


#### 2 设置 GLOBAL 级别变量
设置 GLOBAL 级别变量，重新连接或连接生效；

```sql
-- 登录业务租户
obclient -h10.x.x.x -P2883 -uroot@mytenant#obcluster -p

-- 开启
SET GLOBAL ob_enable_trace_log = ON;
SET GLOBAL ob_enable_plan_cache = ON;

-- 关闭
SET GLOBAL ob_enable_trace_log = OFF;
SET GLOBAL ob_enable_plan_cache = OFF;
```
