---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/OceanBase 查询超时/","dgPassFrontmatter":true}
---


### OceanBase 查询超时
#### 1 OceanBase 查询超时概述

OceanBase 数据库的查询超时功能用于避免长时间不返回的查询长期占用工作线程资源以及可能占用 CPU 资源；OceanBase 数据库租户内部执行查询的工作线程数跟租户的 CPU 个数相关，所以工作线程是很稀有的资源。如果查询长时间不返回，就会一直占用此工作线程资源；

OceanBase 数据库的查询超时时间默认由租户变量 *ob_query_timeout* 控制，默认值是 10000000(单位，微秒)。当查询时间超过该变量值后，会返回错误 -4012(HY000): Timeout；用户可以选择在会话级别调整租户超时变量的值，或者使用 SQL Hint 设置查询超时时间；  
  
  
#### 2 使用 ob_query_timeout 设置查询超时时间  

```sql
-- 查看当前查询超时时间  
SHOW VARIABLES LIKE 'ob_query_timeout';  
SHOW GLOBAL VARIABLES LIKE 'ob_query_timeout';  

-- 调整租户的查询超时时间，其单位为：微秒，该值为：10000000，表示10s；  
SET SESSION ob_query_timeout=1000000; -- 会话级别  
SET GLOBAL ob_query_timeout=1000000; -- 全局，修改全局需谨慎执行；  
```

  
#### 3 使用 SQL Hint 设置查询超时时间  
说明：生效范围仅限于当前 SQL 语句；

```sql
SELECT /*+ QUERY_TIMEOUT(100000000) */ COUNT(*) FROM h1, h2 , h3;
```


### 参考文档


  
  
