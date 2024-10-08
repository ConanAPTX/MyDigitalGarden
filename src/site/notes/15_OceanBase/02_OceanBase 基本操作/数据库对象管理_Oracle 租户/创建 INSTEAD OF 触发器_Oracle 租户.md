---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建 INSTEAD OF 触发器_Oracle 租户/","dgPassFrontmatter":true}
---


### 创建 INSTEAD OF 触发器_Oracle 租户
#### 1 
*INSTEAD OF 触发器* 可以创建在可编辑视图或不可编辑视图上，用于更新视图；*INSTEAD OF 触发器* 可以确定预期的操作并在基础表上执行适当的 DML 操作；

*INSTEAD OF 触发器* 只能是一个行级触发器。INSTEAD OF DML 触发器可以读取 OLD 和 NEW 的值，但不能修改 OLD 和 NEW 的值；


```sql
-- 创建 INSTEAD OF 触发器的语法如下：
	CREATE [OR REPLACE] TRIGGER trigger_name
	INSTEAD OF
	{INSERT | DELETE | UPDATE}
	ON [schema.] view_name
	[FOR EACH ROW]
	[{ FOLLOWS | PRECEDES } other_trigger_name]
	BEGIN
	...
	END;
-- 语句使用说明：
	创建触发器的用户需要拥有 `CREATE TRIGGER` 系统权限。
    OR REPLACE：表示如果要创建的触发器名称已存在，则使用新的定义重新创建触发器。
    view_name：视图名，INSTEAD OF 触发器只能针对视图。
    FOLLOWS | PRECEDES：用于定义触发器顺序；
	    OceanBase 数据库当前版本支持为具有相同触发事件和动作时间的同一个表定义多个触发器，默认情况下，具有相同触发事件和动作时间的触发器其触发顺序不确定。
	    如果希望指定触发器顺序，则可以使用 `FOLLOWS` 来指定新触发器在现有触发器之后激活，暂不支持使用 `PRECEDES` 指定新触发器在现有触发器之前激活，仅语法上支持 `PRECEDES`。
```


#### 2 使用 INSTEAD OF 触发器更新视图

```sql
-- 1.创建表，视图
CREATE TABLE customers(
    customer_id             NUMBER(20) NOT NULL ,
    cust_last_name          VARCHAR(25) DEFAULT NULL,
    cust_first_name         VARCHAR(25) DEFAULT NULL,
    cust_address            VARCHAR(40) DEFAULT NULL,
    cust_nationkey          NUMBER(20) DEFAULT NULL,
    cust_phone              CHAR(15) DEFAULT NULL,
    cust_acctbal            DECIMAL(10,2) DEFAULT NULL,
    cust_mktsegment         CHAR(10) DEFAULT NULL,
    cust_comment            VARCHAR(117) DEFAULT NULL,
    PRIMARY KEY(customer_id));
Query OK, 1 row affected 

CREATE TABLE orders(
    order_id                 NUMBER(20) NOT NULL ,
    customer_id              NUMBER(20) NOT NULL ,
    order_status             CHAR(1) DEFAULT NULL,
    total_price              DECIMAL(10,2) DEFAULT NULL,
    order_date               DATE NOT NULL,
    order_priority           CHAR(15) DEFAULT NULL,
    order_clerk              CHAR(15) DEFAULT NULL,
    order_shippriority       NUMBER(20) DEFAULT NULL,
    order_comment            VARCHAR(79) DEFAULT NULL,
    PRIMARY KEY(order_id,order_date,customer_id));
Query OK, 0 rows affected 

-- 创建视图
CREATE OR REPLACE VIEW order_list AS
     SELECT c.customer_id, c.cust_last_name, c.cust_first_name, o.order_id, o.order_date, o.order_status
     FROM customers c, orders o
     WHERE c.customer_id = o.customer_id;
Query OK, 0 rows affected 
 
-- 2.创建触发器
delimiter /
 
CREATE OR REPLACE TRIGGER order_list_insert
     INSTEAD OF INSERT ON order_list
     DECLARE
       duplicate_info EXCEPTION;
       PRAGMA EXCEPTION_INIT (duplicate_info, -00001);
     BEGIN
       INSERT INTO customers(customer_id, cust_last_name, cust_first_name)
       VALUES (:new.customer_id, :new.cust_last_name, :new.cust_first_name);
     
	   INSERT INTO orders (order_id, order_date, customer_id)
       VALUES (:new.order_id, :new.order_date, :new.customer_id);
     EXCEPTION
       WHEN duplicate_info THEN
         RAISE_APPLICATION_ERROR (
           -20107,
           'Duplicate customer or order ID');
     END order_list_insert;
    /
Query OK, 0 rows affected

delimiter ;

/* 查询并显示要插入的行不存在 */
SELECT COUNT(*) FROM order_list WHERE customer_id = 111;
+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+
1 row in set 

/* 将行数据插入视图 */
INSERT INTO order_list VALUES (111, 'Smith', 'William', 3500, '23-MAR-2001', 0);
Query OK, 1 row affected 

/* 查询并显示该行已插入到视图中 */
SELECT COUNT(*) FROM order_list WHERE customer_id = 111;
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set 

/* 查询并显示该行已插入到 customers 表中 */
SELECT COUNT(*) FROM customers WHERE customer_id = 111;
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set 

/* 查询并显示该行已插入到 orders 表中 */
SELECT COUNT(*) FROM orders WHERE customer_id = 111;
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set 

```





### 参考文档
1. [创建 INSTEAD OF 触发器_V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641871)，；



