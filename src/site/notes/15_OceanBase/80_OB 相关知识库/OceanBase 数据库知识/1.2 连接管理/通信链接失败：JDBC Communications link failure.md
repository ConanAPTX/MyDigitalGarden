---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.2 连接管理/通信链接失败：JDBC Communications link failure/","dgPassFrontmatter":true}
---


### 通信链接失败：JDBC Communications link failure
本文介绍通过 JDBC 连接 OceanBase 数据库失败时如何进行问题排查并解决问题；

#### 1 适用版本
OceanBase 数据库所有版本；

#### 2 问题现象
JDBC 连接 OceanBase 数据库错误，错误信息如下；

```shell
--- Cause: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
The last packet successfully received from the server was 70,810 milliseconds ago. The last packet sent successfully to the server was 5,005 milliseconds ago.
```

#### 3 问题原因
常见的原因如下：
##### 3.1 问题原因 1
连接闲置时间超时。OceanBase 数据库的连接闲置时间默认为 8 小时。该行为由 `interactive_timeout` 和 `wait_timeout` 两个系统变量控制，连接闲置超时时间取两个变量的较小值，有关这两个系统变量的详细信息，请参见 [interactive_timeout](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354785) 和 [wait_timeout](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000035514)，；

##### 3.2 问题原因 2
客户端 SQL 执行耗时长，超过 JDBC 配置的参数 `sockettimeout`。JDBC 参数 `sockettimeout` 内部默认业务配置 `5s`，表示数据库端如果 `5s` 内没有数据返回，则 JDBC 主动停止会话，避免会话被长时间占用；

#### 4 解决方式
-   对于连接闲置时间超时的情况，需要在客户端增加 `ping` 连接的逻辑，不需要在数据库端进行调整；


```sql
-- 查询 interactive_timeout 和 wait_timeout 的值
	obclient> SHOW VARIABLES LIKE 'interactive_timeout';         -- 查询 Session 级别变量
	obclient> SHOW GLOBAL VARIABLES LIKE 'interactive_timeout';  -- 查询 Global 级别变量
	
	obclient> SHOW VARIABLES LIKE 'wait_timeout';                -- 查询 Session 级别变量
	obclient> SHOW GLOBAL VARIABLES LIKE 'wait_timeout';         -- 查询 Global 级别变量
```



-   对于客户端 SQL 执行耗时长的情况，需要对慢 SQL 进行调优或调大 `sockettimeout` 的值；
 


### 参考文档
1. [JDBC Communications link failure](https://www.oceanbase.com/knowledge-base/oceanbase-connector-j-1000000000217896?back=kb)，；
2. [连接异常相关问题的原因与解决方法](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000763298?back=kb)，；


