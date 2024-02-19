---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/闪回/OceanBase 闪回查询/","dgPassFrontmatter":true}
---


### OceanBase 闪回查询
OceanBase 数据库提供了记录级别的闪回查询（Flashback Query）功能，该功能允许用户获取某个历史版本的数据；其中，Oracle 模式支持 `AS OF SCN`和 `AS OF TIMESTAMP` 两种语法来查询；MySQL 模式支持通过 `AS OF SNAPSHOT` 语法来查询；

OceanBase 数据库当前支持用户通过设置租户级配置项 `undo_retention` 的方式来进行闪回查询。当用户设置 `undo_retention` 后，即可使用闪回查询功能查询当前时间 `T` 到 `T - undo_retention` 时间范围内的任意多版本数据。租户级配置项 `undo_retention` 的默认值为 1800，单位为秒，有关租户级该配置项的更多信息，请参见 [undo_retention](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220342)，；

> [!NOTE] 使用租户级配置项 `undo_retention` 进行闪回查询时，需要注意以下事项：
> 1. 假设 `undo_retention` 设置的时间为 T0，设置后对 T0 之前的数据不起作用，对 T0 之后的数据才起作用；
> 2. 如果被查询的表已经被删除进了回收站，则需要先将该表从回收站中恢复；关于恢复回收站对象的相关操作，参见 [恢复回收站对象](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218307)，；
> 3. 假设当前时间为 `T`，设置 `undo_retention` 后，如果在 `T - undo_retention` 时间段内有 DDL 操作，系统会用最新的数据字典解释表的数据。对于 DDL 操作发生前产生的数据，被删除的列的数据将变成删除状态，新添加列的数据则设为 Default 值；
> 4. 闪回查询需要额外的存储空间来保留历史数据，如果调大 `undo_retention` 会导致存储空间的放大；建议调大 `undo_retention` 后，留意存储空间的变化；


#### 1 前提条件
在通过租户级配置项 `undo_retention` 进行闪回查询前，需要先修改 `undo_retention` 的值；`undo_retention` 的默认值为 `1800`，单位为秒；

1. `undo_retention = 0`
	1. 表示系统应保留的多版本数据范围，默认值为 1800，单位为秒，在转储时控制多版本数据的回收；
	2. 该参数类型为 int，取值范围：0，2^32^，生效范围：Global；
	3. 当 `undo_retention` 的值为 `0` 时，表示未开启多版本转储，即转储文件仅保留当前最新版本的行数据；
	4. 当 `undo_retention` 的值大于 `0` 时，表示开启多版本转储，并且转储文件保留这段时间（单位为秒）以内的多版本行数据；
	5. 详细情况请查看：[undo_retention](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946115)，；

```sql
-- 1. 租户管理员登录到数据库，MySQL 租户的管理员用户为 root 用户，Oracle 租户的管理员用户为 SYS 用户；
obclient -h10.xx.xx.xx -P2883 -uroot@mysql#demo -p***** -A

-- 2. 修改 undo_retention 变量的值  
obclient> SET GLOBAL undo_retention=900;  
```

有关租户级配置项 `undo_retention` 的更多信息，请参见 [undo_retention](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220342)，；

#### 2 MySQL 模式下的闪回查询

```sql
-- 1. MySQL 租户登录到数据库
obclient -h10.xx.xx.xx -P2883 -uinfo@mysql#demo -p***** -A

-- 2. 对表执行闪回查询
-- 通过 AS OF SNAPSHOT 指定历史时间并闪回查询单表在该历史时间点的状态的数据；
SELECT * FROM table1 AS OF SNAPSHOT 1597306800000000000; 
	-- 1597306800000000000 为时间戳，需要您根据待查询的时间自行转换，单位为微秒；

-- 可以根据需要将待查询的时间转换为时间戳格式；
SELECT time_to_usec('2020-08-13 16:20:00') * 1000;
	-- 输出结果：1597306800000000000
```

#### 3 Oracle 模式下的闪回查询

```sql
-- 1. Oracle 租户登录到数据库
obclient -h10.xx.xx.xx -P2883 -uinfo@oracle#demo -p***** -A

-- 2. Oracle 模式下的闪回查询，对表执行闪回查询
-- 2.1 通过 TIMESTAMP 指定的历史时间并闪回查询一张单表在该历史时间中的状态的数据
obclient [SYS]> SELECT * FROM table1 AS OF TIMESTAMP TO_TIMESTAMP('2020-08-13 16:20:00','yyyy-mm-dd hh24:mi:ss');

-- 2.2 通过 TIMESTAMP 指定的历史时间并闪回查询多表在该历史时间中的状态的数据
obclient [SYS]> SELECT * FROM table1, table2 AS OF TIMESTAMP TO_TIMESTAMP('2020-08-13 16:20:00','yyyy-mm-dd hh24:mi:ss');
obclient [SYS]> SELECT * FROM table1 AS OF TIMESTAMP expr1, table2 AS OF TIMESTAMP TO_TIMESTAMP('2020-08-13 16:20:00','yyyy-mm-dd hh24:mi:ss');

-- 2.3 通过 SCN 指定历史时间并闪回查询单表在该历史时间点的状态的数据
obclient [SYS]> SELECT * FROM table1 AS OF SCN 1597306800000000000;

-- 可以根据需要将待查询的时间转换为时间戳格式
SELECT (to_date('2020-08-13 16:20:00','yyyy-mm-dd hh24:mi:ss') - to_date('1970-01-01 08:00:00', 'yyyy-mm-dd hh24:mi:ss')) * 86400 * 1000 * 1000 * 1000 AS unix_nsec_timestamp FROM DUAL;
	-- 输出结果：1597306800000000000
```


### 参考文档
1. [闪回查询_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218071)，；

