---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase 闪回查询_MySql，Oracle 模式/","dgPassFrontmatter":true}
---


### OceanBase 闪回查询
OceanBase 数据库提供了记录级别的闪回查询（Flashback Query）功能，该功能允许用户获取某个历史版本的数据；

其中，Oracle 模式支持 `AS OF SCN`和 `AS OF TIMESTAMP` 两种语法来查询；MySQL 模式支持通过 `AS OF SNAPSHOT` 语法来查询；


#### 1 设置 undo_retention 变量
OceanBase 数据库当前支持用户通过设置租户级配置项 `undo_retention` 的方式来进行闪回查询。当用户设置该配置项后，即可使用闪回查询功能查询当前时间 `T` 到 `T - undo_retention` 时间范围内的任意多版本数据；

> [!NOTE] 使用租户级配置项 `undo_retention` 进行闪回查询时，需要注意以下事项：
> 1. 假设 `undo_retention` 设置的时间为 T0，设置后对 T0 之前的数据不起作用，对 T0 之后的数据才起作用；
> 2. 如果被查询的表已经被删除进了回收站，则需要先将该表从回收站中恢复；
> 3. 假设当前时间为 `T`，设置 `undo_retention` 后，如果在 `T - undo_retention` 时间段内有 DDL 操作，系统会用最新的数据字典解释表的数据。对于 DDL 操作发生前产生的数据，被删除的列的数据将变成删除状态，新添加列的数据则设为 Default 值；
> 4. 闪回查询需要额外的存储空间来保留历史数据，如果调大 `undo_retention` 会导致存储空间的放大；建议调大 `undo_retention` 后，留意存储空间的变化；

> [!NOTE] 注意：
> 租户开启多版本转储后，大版本合并会保留对应的增量转储文件，但 Major SSTable 中不会存放多版本数据。建议该参数不要设置过大，防止因参数过大而导致保留的 SSTable 数量超限；

##### 1.1 V2.x，V3.x 版本
1. `undo_retention`：表示系统应保留的多版本数据范围，在转储时控制多版本数据的回收；【*该配置项从 V2.1 版本开始以`系统变量`的参数类型引入*】
	1. 【*GLOBAL*】【int】；默认值为 1800，单位为秒，取值范围为: [0, 2^32^)；
	2. OB 数据库转储的数据会保留多个版本的历史数据行，可以通过 undo_retention 来控制转储中保留的多版本数据范围；其中：
		1. 当 undo_retention 的值为 0 时，表示未开启多版本转储，即转储文件仅保留当前最新版本的行数据；
		2. 当 undo_retention 的值大于 0 时，表示开启多版本转储，并且转储文件保留这段时间（单位为秒）以内的多版本行数据；
	3. 租户开启多版本转储后，大版本合并会保留对应的增量转储文件，但 Major SSTable 中不会存放多版本数据；
	4. 建议该参数不要设置过大，防止因参数过大而导致保留的 SSTable 数量超限；根据需要设置，一般保持默认值；
2. 有关该配置项的更多信息，请参见：*undo_retention*：[V3.4.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944696)，；

```sql
-- V2.x，V3.x 版本
-- 1. 租户管理员登录到数据库，MySQL 租户的管理员用户为 root 用户，Oracle 租户的管理员用户为 SYS 用户；
obclient -h10.xx.xx.xx -P2883 -uroot@mysql#demo -p***** -A

-- 2. 修改 undo_retention 变量的值  
obclient> SET GLOBAL undo_retention=900;  
```


##### 1.2 V4.x 版本
在通过租户级配置项 `undo_retention` 进行闪回查询前，需要先修改该配置项的值；

1. `undo_retention`：用于设置系统应保留的多版本数据范围，在转储时控制多版本数据的回收；【从 V4.0.0 版本开始，参数类型由系统变量调整为`配置项`；】
	1. 【_租户级_】【INT】；默认值为 1800，单位为秒，取值范围为：[0, 4294967295]；动态生效；
	2. OceanBase 数据库转储的数据会保留多个版本的历史数据行，您可以通过 `undo_retention` 来控制转储中保留的多版本数据范围；
		1. 当该配置项的值为 `0` 时，表示未开启多版本转储，即转储文件仅保留当前最新版本的行数据；
		2. 当该配置项的值大于 `0` 时，表示开启多版本转储，并且转储文件保留这段时间（单位为秒）以内的多版本行数据；
2. 有关该配置项的更多信息，请参见：*undo_retention*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220342)，[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052395)，；

```sql
-- 系统租户(即sys租户)和普通租户均可以查询集群级配置项的值 
SHOW PARAMETERS LIKE 'undo_retention';

ALTER SYSTEM SET undo_retention=1800;
```


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
2.  


