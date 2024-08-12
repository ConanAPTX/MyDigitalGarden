---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/EXISTS 条件/","dgPassFrontmatter":true}
---


### EXISTS 条件

EXISTS 条件（EXISTS Condition）用于测试子查询中是否存在指定的行；

```sql
/*
EXISTS 条件语法：
	EXISTS (subquery)
使用说明：
	如果子查询至少返回一行，则说明存在您想要的数据；
*/

-- 使用示例
SELECT dept_id FROM dept d 
WHERE EXISTS (
	SELECT * FROM emp e WHERE d.dept_id = e.dept_id
) 
ORDER BY dept_id;
```


### 参考文档
1. *EXISTS 条件*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821938)，；


