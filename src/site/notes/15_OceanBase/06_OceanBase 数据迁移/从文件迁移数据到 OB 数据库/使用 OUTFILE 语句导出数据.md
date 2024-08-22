---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/从文件迁移数据到 OB 数据库/使用 OUTFILE 语句导出数据/","dgPassFrontmatter":true}
---



### 使用 OUTFILE 语句导出数据
`SELECT INTO OUTFILE` 语句常用的一种数据导出方式。 `SELECT INTO OUTFILE` 语句能够对需要导出的字段做出限制，这很好的满足了某些不需要导出主键字段的场景。配合 `LOAD DATA INFILE` 语句导入数据，是一种很便利的数据导入导出方式；

#### 1 背景信息
OceanBase 数据库兼容这一个语法：

| 模式        | 建议使用的 OceanBase 数据库版本 | 建议使用的客户端              |
| --------- | --------------------- | --------------------- |
| MySQL 模式  | V2.2.40 及以上           | MySQL Client、OBClient |
| Oracle 模式 | V2.2.40 及以上           | OBClient              |

> [!NOTE] *注意*：
> 客户端需要直连 OceanBase 数据库实例以做导入导出操作；

#### 2 语法
语法详细情况可以查看官方文档；


#### 3 使用示例
1. 在租户 `mysql001` 的 `test` 库中创建表 `tbl1` 并插入数据；
```sql
obclient [test]> CREATE TABLE tbl1(col1 INT PRIMARY KEY,col2 varchar(128),col3 INT);
Query OK, 0 rows affected

obclient [test]> INSERT INTO tbl1 VALUES(1,'one',80),(2,'two',90),(3,'three',100);
Query OK, 3 rows affected
Records: 3  Duplicates: 0  Warnings: 0

obclient [test]> SELECT * FROM tbl1;
+------+-------+------+
| col1 | col2  | col3 |
+------+-------+------+
|    1 | one   |   80 |
|    2 | two   |   90 |
|    3 | three |  100 |
+------+-------+------+
3 rows in set
```

2. 设置导出的文件路径；
设置 *secure_file_priv* 配置项详细情况：[[15_OceanBase/05_OceanBase 性能调优/系统调优/secure_file_priv 配置项\|secure_file_priv 配置项]]，；

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/06-ocean-base/ob/load-data/#2-2-secure-file-priv" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 2.2 secure_file_priv 配置项
设置 _secure_file_priv_ 配置项详细情况：[[15_OceanBase/05_OceanBase 性能调优/系统调优/secure_file_priv 配置项\|secure_file_priv 配置项]]，；
```sql
-- 1.登录到要连接的 OBServer 节点。
ssh admin@xxx.xxx.xxx.xxx

-- 2.执行以下命令，通过本地 Unix Socket 连接方式连接租户 `mysql001`。
obclient -S /home/admin/oceanbase/run/sql.sock -uroot@mysql001 -p******
 
-- 3.设置导出路径为 `/home/admin`；
SHOW VARIABLES like 'SECURE_FILE_PRIV';        -- sys 租户和所有用户租户均可以查询
obclient [(none)]> SET GLOBAL secure_file_priv = "/home/admin";
Query OK, 0 rows affected

-- 4. 对待导入文件所在的目录进行授权
chmod -R 755 /home/admin/test         -- 假设导入文件所在的目录为 `/home/admin/test`；
```



</div></div>



3. 使用 `SELECT INTO OUTFILE` 语句导出表数据
重新连接数据库后，使用 `SELECT INTO OUTFILE` 语句导出表 `tbl1` 中的数据。导出文件名为 `tbl1.sql`；指定逗号作为两个字段值之间的标志；对字符串类型的值使用 `"` 字符包裹；使用换行符作为结束标志；

```sql
SELECT * 
	INTO OUTFILE '/home/admin/tbl1.sql' FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' LINES TERMINATED BY '\n' 
FROM tbl1;
Query OK, 3 rows affected
```

4. 登录机器，在设备本地的 `/home/admin` 目录下查看导出的文件信息。
```shell
[xxx@xxx /home/admin]# cat tbl1.sql
1,"one",80
2,"two",90
3,"three",100
```


> [!NOTE] 注意：
> 通过 `SELECT INTO OUTFILE` 方法导出的文件，可以通过 `LOAD DATA` 语句进行导入 ；





### 参考文档
1. *使用 OUTFILE 语句导出数据*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218020)，；


