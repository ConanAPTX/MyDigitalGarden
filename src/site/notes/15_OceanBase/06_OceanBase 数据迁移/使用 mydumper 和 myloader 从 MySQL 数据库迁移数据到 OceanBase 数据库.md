---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/使用 mydumper 和 myloader 从 MySQL 数据库迁移数据到 OceanBase 数据库/","dgPassFrontmatter":true}
---


### 使用 mydumper 和 myloader 从 MySQL 数据库迁移数据到 OceanBase 数据库
#### 1 环境准备
安装 mydumper，myloader 工具请参考：[[15_OceanBase/06_OceanBase 数据迁移/安装 mydumper，myloader 工具\|安装 mydumper，myloader 工具]]，；

#### 2 备份操作

```shell
# 使用 mydumper 对 MySql 数据库进行备份
mydumper -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p****** -o /data/backup/mysql/    # 备份全库          
mydumper -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p****** -B test -o /data/backup/mysql/            # 备份指定数据库 test
mydumper -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p****** -B test -T t1,t2 -o /data/backup/mysql/   # 备份指定数据库指定表(t1,t2)
mydumper -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p****** -B test -T t1 -m -o /data/backup/mysql/   # 仅备份表数据
mydumper -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p****** -B test -T t1 -t 6 -c -o /data/backup/mysql/  # 备份 t1 表的数据，开并行和数据压缩
```
`mydumper` 详细语法请参考：[[11_MySQL/04_安全与保护/MySql 备份恢复/mydumper，myloader 概述\|mydumper，myloader 概述]] ，；

#### 3 恢复操作
##### 3.1 禁用外键检查约束
在备份的表结构语句里，可能包含外键。在导入 OceanBase MySQL 里时，如果外键依赖的表没有创建，导入脚本会报错，因此在导入之前需要禁用外键检查约束；

```sql
MySQL [oceanbase]> SET GLOBAL foreign_key_checks = off;
Query OK, 0 rows affected

MySQL [oceanbase]> SHOW GLOBAL VARIABLES LIKE '%foreign%';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| foreign_key_checks | OFF   |
+--------------------+-------+
1 row in set
```

##### 3.2 恢复数据到 OceanBase 数据库
###### 3.2.1 使用 source 命令恢复数据
若无法直接还原单表，可以找到对应的单表 sql 文件，进入命令行，使用 `source` 命令恢复数据；

```sql
source test.t1-schema.sql    -- 还原表结构

source test.t1.00000.sql     -- 还原表数据
```

###### 3.2.2 使用 myloader 命令恢复数据
```shell
# 使用 myloader 命令恢复数据
myloader -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p******  -B test -o -d /data/backup/mysql/  # 导入表结构和数据
myloader -h xx.xx.xx.xx -P2883 -u 'user@tenantname#clustenamer' -p******  -B test -s test1 -o -d /data/backup/mysql/  # 导入数据库，若目标库不存在则会新建
```
`myloader` 详细语法请参考：[[11_MySQL/04_安全与保护/MySql 备份恢复/mydumper，myloader 概述\|mydumper，myloader 概述]] ，；

### 参考文档
1. [使用 mydumper 和 myloader 从 MySQL 数据库迁移数据到 OceanBase 数据库](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218006)，；






