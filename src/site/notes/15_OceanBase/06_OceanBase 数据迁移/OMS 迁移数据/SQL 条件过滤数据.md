---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/SQL 条件过滤数据/","dgPassFrontmatter":true}
---


### SQL 条件过滤数据
#### 1 适用版本
OMS 3.3.1 版本开始支持；

#### 2 适用场景
创建数据迁移或数据同步项目时，您可以设置 SQL 过滤条件来过滤数据。完成设置后，只有满足过滤条件的数据才会被迁移或同步至目标数据库。该功能适用于数据的定期同步或迁移、拆分数据表、过滤历史数据和脏数据等多种应用场景；

#### 3 使用限制
> [!NOTE] 使用限制：
> -   通过 SQL 条件过滤数据时，仅支持过滤当前表的字段，不支持跨表过滤；
> -   SQL 条件过滤数据的作用范围为全量迁移/全量同步和增量同步；
> -   SQL 条件过滤数据使用的列请勿执行 DDL 变更，否则可能导致数据迁移异常；


#### 4 操作步骤
详细操作步骤可以查看参考文档；


### 参考文档
1. *SQL 条件过滤数据*：[V4.2.2](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613497)，；


