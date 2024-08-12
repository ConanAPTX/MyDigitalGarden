---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建 DML 触发器_Oracle 租户/","dgPassFrontmatter":true}
---


### 创建 DML 触发器_Oracle 租户
在表或视图上可以创建 DML 触发器，其触发事件由 DML 语句 `DELETE`、`INSERT` 和 `UPDATE` 组成；

触发器名与存储过程名称和包的名字不一样，它是单独的名字空间，因而触发器名称可以和表或存储过程有相同的名字。创建触发器的用户需要拥有 `CREATE TRIGGER` 系统权限；

#### 1 创建行级触发器
OceanBase 数据库当前版本(V4.3.0)支持创建行级触发器，所包含的类型如下：`BEFORE INSERT FOR EACH ROW`， `AFTER INSERT FOR EACH ROW`， `BEFORE UPDATE FOR EACH ROW`，`AFTER UPDATE FOR EACH ROW`，`BEFORE DELETE FOR EACH ROW`，`AFTER DELETE FOR EACH ROW`；

创建行级触发器的语法请查看参考文档；


```sql
创建行级触发器的语法:
	CREATE [OR REPLACE] TRIGGER trigger_name
	{BEFORE | AFTER }
	{INSERT | DELETE | UPDATE [OF column [, column ...]]}
	ON [schema.] table_name 
	[REFERENCING {OLD [AS] old | NEW [AS] new| PARENT as parent}]
	FOR EACH ROW
	[{ FOLLOWS | PRECEDES } other_trigger_name]
	[WHEN condition]
	BEGIN
	...
	END;
参数解释如下表：
	`BEFORE` 和 `AFTER`：指出触发器的触发时序分别为前触发和后触发方式。
		前触发是在执行触发事件之前触发当前所创建的触发器；后触发是在执行触发事件之后触发当前所创建的触发器。
	FOR EACH ROW：说明触发器为行触发器。
		行触发器要求当一个 DML 语句操作影响数据库中的多行数据时，对于其中的每个数据行，只要它们符合触发约束条件，均激活一次触发器。
	REFERENCING：说明相关名称，在行触发器的 PL 块和 `WHEN` 子句中可以使用相关名称参照当前的新、旧列值。默认的相关名称分别为 `OLD` 和 `NEW`。
		触发器的 PL 块中应用相关名称时，必须在它们之前加冒号（:），但在 `WHEN` 子句中不能加冒号。
	WHEN：说明触发约束条件。
		Condition 为一个逻辑表达式时，其中必须包含相关名称，而不能包含查询语句，也不能调用 PL 函数。`WHEN` 子句指定的触发约束条件只能用在 `BEFORE` 和 `AFTER` 行触发器中。
	FOLLOWS 和 PRECEDES：用于定义触发器顺序；
		OceanBase 数据库当前版本支持为具有相同触发事件和动作时间的同一个表定义多个触发器，默认情况下，具有相同触发事件和动作时间的触发器其触发顺序不确定。
		如果希望指定触发器顺序，则可以使用 `FOLLOWS` 来指定新触发器在现有触发器之后激活，暂不支持使用 `PRECEDES` 指定新触发器在现有触发器之前激活，仅语法上支持 `PRECEDES`。
```


> [!NOTE] 注意：
> 当一个基表被修改（`INSERT`、`UPDATE` 或 DELETE）时要执行存储过程，执行触发器时根据其所依附的基表改动而自动触发，因此与应用程序无关，使用触发器可以保证数据的一致性和完整性；


##### 1.1 行级触发器的使用限制
> [!NOTE] `行级触发器的使用限制`：  
> 1. 行级触发器内部的 SELECT 语句只能为 SELECT ... INTO ... 结构，或者为定义游标所使用的 SELECT 语句；  
> 2. 行级触发器中不能使用数据库事务控制语句 COMMIT，ROLLBACK 和 SAVEPOINT 语句；  
> 3. 由行级触发器调用的存储过程或函数也不能使用数据库事务控制语句；  

##### 1.2 OLD 和 NEW 伪记录  
当行触发器被触发时，要引用被插入（INSERT）、更新（UPDATE）或删除（DELETE）的记录中的列值，有时要使用操作前或操作后列的值，
> 语法如下：
> 1. `:NEW`，修饰符表示访问操作完成后列的值；  
> 2. `:OLD`，修饰符表示访问操作完成前列的值；  

| 特性   | INSERT | UPDATE | DELETE |
| :------------- | :---------- | :------------ | :------------ |
| OLD |   NULL   | 有效 | 有效 |
| NEW |   有效    | 有效 | NULL |

##### 1.3 示例

创建一个简单的行级触发器；
```sql
-- 1.创建表并插入测试数据
CREATE TABLE regions( region_id NUMBER(5,0), region_name  VARCHAR(50));
INSERT INTO regions VALUES(1,'Europe'),(4,'Middle East and Africa'),(3,'Asia'),(2,'Americas'); 
CREATE TABLE reg_his AS SELECT * FROM regions WHERE 1=2;   -- 复制表(该语法支持复制数据)

-- 2.创建行级触发器
CREATE OR REPLACE TRIGGER del_new_region
      BEFORE DELETE ON regions 
      FOR EACH ROW
      WHEN (old.region_id >3)
   BEGIN
      INSERT INTO reg_his(region_id , region_name )
           VALUES( :old.region_id, :old.region_name );
   END;
 /

-- 3.使用测试
上述示例中，。
obclient>SELECT * FROM regions;   -- 有数据
obclient>SELECT * FROM reg_his;   -- 空表

-- 当对 regions 表的数据进行删除操作时，把 region_id 大于 3 的记录移动到 reg_his 表中
DELETE FROM regions WHERE region_id>2;   -- 删除数据

SELECT * FROM regions;
+-----------+-------------+
| REGION_ID | REGION_NAME |
+-----------+-------------+
|         1 | Europe      |
|         2 | Americas    |
+-----------+-------------+

SELECT * FROM reg_his;
+-----------+------------------------+
| REGION_ID | REGION_NAME            |
+-----------+------------------------+
|         4 | Middle East and Africa |
+-----------+------------------------+
```


#### 2 创建语句级触发器  
语句级触发器即表级触发器；语句级触发器是将整个语句操作作为触发事件，当它符合约束条件时，激活一次触发器。如果您在表上针对某个 DML 操作创建了语句触发器，则当执行 DML 操作时，系统就会自动执行触发器的相应语句；  

```sql
创建语句级触发器的语法如下：
	CREATE [OR REPLACE] TRIGGER trigger_name
	{BEFORE | AFTER }
	{INSERT | DELETE | UPDATE [OF column [, column ...]]}
	ON [schema.] table_name 
	BEGIN
	...
	END;
```


##### 2.1 语句级触发器的使用限制
> [!NOTE] `语句级触发器的使用限制`：  
> 1. 语句级触发器不能使用 OLD 和 NEW 访问操作前或操作后的数据；
> 2. 语句级触发器不能使用 WHEN 子句；


##### 2.2 示例

创建一个表，用来记录新表的删除数据；

```sql
-- 1.创建表 student 和表 old_student， 表 old_student 用来记录表 student 删除的数据  
CREATE TABLE student(id VARCHAR2(4) NOT NULL,name VARCHAR2(15) NOT NULL,age NUMBER(2) NOT NULL,sex VARCHAR2(15) NOT NULL);  
CREATE TABLE old_student AS SELECT * FROM student;  -- 复制表(该语法支持复制数据)
INSERT INTO student VALUES('01','a',6,'Male'),('02','b',7,'Female');  

SELECT * FROM student;  
SELECT * FROM old_student;  
  
-- 2.创建语句级触发器 TIG_OLD_STU  
obclient> delimiter /  
obclient> CREATE OR REPLACE TRIGGER TIG_OLD_STU  
BEFORE DELETE ON student  
BEGIN  
INSERT INTO old_student SELECT * FROM student;  
END;  
/  
obclient> delimiter ;  
  
-- 3.测试：删除表 student 中的数据后，再查看 2 个表的数据  
DELETE student;  
SELECT * FROM student;  
SELECT * FROM old_student;
```

### 参考文档
1. [创建 DML 触发器_Oracle 租户_V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641872)，；