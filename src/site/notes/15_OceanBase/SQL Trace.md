---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/SQL Trace/","dgPassFrontmatter":true}
---


### SQL Trace
#### 1 

设置变量 *ob_enable_trace_log* / *ob_enable_plan_cache*；

> [!NOTE] `注意`：
> 建议开启 plan cache 之前，先开启 SQL Audit；

#### 2 设置 Session 级别变量
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


#### 3 设置 GLOBAL 级别变量
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


### 参考文档



