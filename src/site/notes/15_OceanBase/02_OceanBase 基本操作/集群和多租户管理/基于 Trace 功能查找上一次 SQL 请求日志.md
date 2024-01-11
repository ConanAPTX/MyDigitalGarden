---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/基于 Trace 功能查找上一次 SQL 请求日志/","dgPassFrontmatter":true}
---


### 基于 Trace 功能查找上一次 SQL 请求日志

OceanBase 数据库支持基于 Trace 功能快速获取上一次 SQL 请求的完整日志；
为了便于查找目标 SQL 请求日志，您可提前通过设置日志打印级别来减少 SQL 模块之外的日志的打印。有关日志打印级别的相关设置请参见 [设置日志打印级别](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702460);

#### 1 开启 Trace 功能
可通过如下两种方式来开启 Trace 功能：
##### 1.1 方式 1：
通过设置 Hint 中的 `trace_log` 字段来开启 Trace 功能。该方式只对携带 Hint 的当前语句生效。更多 Hint 语句介绍，请参见 [Optimizer Hint](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702247)；
```sql
-- 通过设置 Hint中的 trace_log 字段来开启 Trace 功能；
-- 方法1：
SELECT /*+trace_log=on*/c1 FROM t1 LIMIT 2; 
```

##### 1.2 方式 2：
通过设置 Session 变量 `ob_enable_show_trace` 来开启 Trace 功能。这种方式对本 Session 的后续所有语句生效；
```sql
-- 方法2：
-- v4.0.0 以下版本，通过设置 Session 变量 ob_enable_trace_log 来开启 Trace 功能；
 SHOW VARIABLES LIKE 'ob_enable_trace_log';
 SET ob_enable_trace_log='ON';   -- 该方式对本 Session 的后续所有语句生效

-- v4.0.0 及以上版本
 SHOW VARIABLES LIKE 'ob_enable_show_trace';
 obclient [test]> SET ob_enable_show_trace=ON;
```

1. `ob_enable_trace_log`：用于设置是否使用 trace 日志；
	1. 该变量详细情况请参考：[ob_enable_trace_log 变量](https://www.yuque.com/conan-vw2ov/uzu4yl/qcigl9iskd0657xb)，；
2. `ob_enable_show_trace`：用于设置是否使用 show trace 日志；
	1. 该变量详细情况请参考：[ob_enable_show_trace 变量](https://www.oceanbase.com/docs/common-oceanbase-database-cn-0000000002183535)，；


#### 2 获取上一次 SQL 请求日志的 trace_id
开启 Trace 功能并执行 SQL 请求后，通过 `SHOW TRACE` 语句可获取上一次 SQL 请求日志的 trace_id ；
```sql
SHOW TRACE;
```
该语句输出详细情况请参考：[基于 Trace 功能查找上一次 SQL 请求日志](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702462)，；

#### 3 通过 trace_id 在日志文件查询上一次 SQL 请求的完整日志

OceanBase 数据库日志打印时均会携带 `trace_id` ，通过在日志文件（ `observer.log`、`election.log` 和 `rootservice.log` ）中搜索对应的 `trace_id` ，可以获取上一次 SQL 请求的完整日志；

##### 3.1 通过 `last_trace_id()` 获取 `trace_id`

```sql
obclient [test]> SELECT last_trace_id();
+-----------------------------------+
| last_trace_id()                   |
+-----------------------------------+
| YB42AC1E87CC-0005F6BFDB3E2199-0-0 |
+-----------------------------------+
1 row in set (0.000 sec)
```


##### 3.2 获取 `trace_id` 所对应的日志
```shell
# 登录对应 OBServer 节点，进入到日志文件所在目录
ssh 100.64.174.102
cd /home/admin/oceanbase/log   

# 获取 trace_id 所对应的日志，执行 grep $trace_id observer.log 命令
grep YB426440AE66-0005F28319F16B2A-0-0  observer.log
	# 获取 trace_id 为 YB426440AE66-0005F28319F16B2A-0-0 的日志；
```


### 参考文档：
1.  [基于 Trace 功能查找上一次 SQL 请求日志](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946340)，；