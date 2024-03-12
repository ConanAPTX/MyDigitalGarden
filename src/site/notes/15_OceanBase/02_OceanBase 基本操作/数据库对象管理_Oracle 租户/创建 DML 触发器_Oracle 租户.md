---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建 DML 触发器_Oracle 租户/","dgPassFrontmatter":true}
---


### 创建 DML 触发器_Oracle 租户
#### 1 创建行级触发器

> [!NOTE] `行级触发器的使用限制`：  
> 1. 行级触发器内部的 SELECT 语句只能为 SELECT ... INTO ... 结构，或者为定义游标所使用的 SELECT 语句；  
> 2. 行级触发器中不能使用数据库事务控制语句 COMMIT，ROLLBACK 和 SAVEPOINT 语句；  
> 3. 由行级触发器调用的存储过程或函数也不能使用数据库事务控制语句；  

##### 1.1 OLD 和 NEW 伪记录  
当行触发器被触发时，要引用被插入（INSERT）、更新（UPDATE）或删除（DELETE）的记录中的列值，有时要使用操作前或操作后列的值，语法如下：
NEW 修饰符表示访问操作完成后列的值；  
OLD 修饰符表示访问操作完成前列的值；  

| 特性   | INSERT | UPDATE | DELETE |
| :------------- | :---------- | :------------ | :------------ |
| OLD |   NULL   | 有效 | 有效 |
| NEW |   有效    | 有效 | NULL |

  

#### 2 创建语句级触发器  
语句级触发器即表级触发器。语句级触发器是将整个语句操作作为触发事件，当它符合约束条件时，激活一次触发器。如果您在表上针对某个 DML 操作创建了语句触发器，则当执行 DML 操作时，系统就会自动执行触发器的相应语句；  

> [!NOTE] `语句级触发器的使用限制`：  
> 1. 语句级触发器不能使用 OLD 和 NEW访问操作前或操作后的数据；  
> 2. 语句级触发器不能使用 WHEN 子句；  

```sql
-- 创建表 student 和表 old_student， 表 old_student 用来记录表 student 删除的数据  
CREATE TABLE student(id VARCHAR2(4) NOT NULL,name VARCHAR2(15) NOT NULL,age NUMBER(2) NOT NULL,sex VARCHAR2(15) NOT NULL);  
CREATE TABLE old_student AS SELECT * FROM student;  
  
INSERT INTO student VALUES('01','a',6,'Male'),('02','b',7,'Female');  
  
SELECT * FROM student;  
SELECT * FROM old_student;  
  
-- 创建语句级触发器 TIG_OLD_STU  
delimiter /  
CREATE OR REPLACE TRIGGER TIG_OLD_STU  
BEFORE DELETE ON student  
BEGIN  
INSERT INTO old_student SELECT * FROM student;  
END;  
/  
delimiter ;  
  
-- 删除表 student 中的数据后，再查看 2 个表的数据  
DELETE student;  
SELECT * FROM student;  
SELECT * FROM old_student;
```

### 参考文档