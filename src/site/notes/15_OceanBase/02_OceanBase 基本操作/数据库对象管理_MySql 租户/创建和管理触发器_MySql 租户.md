---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建和管理触发器_MySql 租户/","dgPassFrontmatter":true}
---


### 创建和管理触发器_MySql 租户
#### 1 触发器概述  
OceanBase 数据库 MySQL 模式兼容 MySQL 5.6 的触发器。触发器是与表相关的数据库对象，并在表发生特定事件时被激活；  
触发器可以在语句插入、更新或删除关联表中的行时被激活。例如，可以通过 INSERT 或 LOAD DATA 语句插入行，并且每插入一行就会激活一次 INSERT 触发器。若批量插入两行数据，则会出发两次触发器；  

##### 1.1 触发器的分类  
MySQL 租户当前主要支持以下类型的触发器：INSERT 型触发器，UPDATE 型触发器，DELETE 型触发器；  
`INSERT 型触发器`：表示插入某一行时激活触发器，可以通过 INSERT、LOAD DATA、REPLACE 语句触发；  
`UPDATE 型触发器`：表示更改某一行时激活触发器，可以通过 UPDATE 语句触发；  
`DELETE 型触发器`：删除某一行时激活触发器，可以通过 DELETE、REPLACE 语句触发；  

比较特殊的是 `INSERT INTO ... ON DUPLICATE KEY UPDATE` 语句，每个行都会激活一个 `BEFORE INSERT` 触发器，接着是 `AFTER INSERT` 触发器或者 `BEFORE UPDATE` 与 `AFTER UPDATE` 触发器，是 `AFTER INSERT` 触发器还是 `BEFORE UPDATE` 与 `AFTER UPDATE` 触发器主要取决于行是否有重复的键；

> [!NOTE] OceanBase 数据库中也定义了 *NEW.columnName* 和 *OLD.columnName*：  
> 1. 在 INSERT 型触发器中，NEW.columnName 用来表示将要（BEFORE）或已经（AFTER）插入的新数据。其中，columnName 为相应数据表的某一列名；  
> 2. 在 UPDATE 型触发器中，OLD.columnName 用来表示将要或已经被修改的原数据，NEW.columnName 用来表示将要或已经修改后的新数据；
> 3. 在 DELETE 型触发器中，OLD.columnName 用来表示将要或已经被删除的原数据；
> 4. OLD.columnName 是只读的，而 NEW.columnName 则可以在触发器中使用 SET 赋值；  

#### 2 查看触发器信息  
```sql
-- 通过 SHOW 语句查看触发器信息  
SHOW TRIGGERS; -- 查看当前数据库的触发器信息  
SHOW TRIGGERS FROM database_name; -- 查看指定数据库的触发器信息  
  
-- 通过视图查看触发器信息  
SELECT * FROM information_schema.triggers where trigger_name = 'xx';  
```


#### 3 删除触发器  
```sql
/*  
语法：DROP TRIGGER [IF EXISTS] [database_name.]trigger_name;  
参数详解：  
	IF EXISTS 表示如果创建的触发器已经存在，则重新创建触发器；
	database_name 表示数据库名称，是可选参数，如果不指定则表示从当前数据库中删除触发器；
*/ 

-- 删除触发器
DROP TRIGGER test.test_trg;            -- 删除触发器
DROP TRIGGER IF EXISTS test.test_trg;  -- 删除触发器
```


#### 4 创建触发器  
> [!NOTE] `MySQL 模式的触发器有以下使用限制`：  
> 1. 只能在永久表上创建触发器，不能对临时表创建触发器；  
> 2. 触发器不能使用 CALL 语句调用将数据返回给客户端或使用动态 SQL 的存储过程，但是允许存储过程或者函数通过 OUT 或者 INOUT 类型的参数将数据返回触发器；  
> 3. 触发器中不能使用开启或结束事务的语句段，例如，开始事务（START TRANSACTION）、提交事务（COMMIT）或回滚事务（ROLLBACK），但是可以回滚到一个保存点，即 Savepoint 是允许的，因为回滚到保存点不会结束事务；  
> 4. 外键不会激活触发器；  
> 5. 触发器中不允许返回值，因此触发器中不能有返回语句，如果需要立即停止一个触发器，则需要使用 LEAVE 语句；  

##### 4.1 创建 DML 触发器  
更多详细情况：`创建 DML 触发器`：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220222)，[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641919)，；

> [!NOTE] `前提条件`：创建触发器的用户需要具备以下权限：  
> 1. 当前触发器关联的表的权限，包括 SELECT、INSERT、UPDATE、DELETE 等权限；  
> 2. 触发器权限，即 CREATE TRIGGER 权限；  
> 3. 触发器激活后要执行的语句的权限；  

```sql
/*  
语法：  
	CREATE TRIGGER trigger_name trigger_time trigger_event  
	ON tbl_name FOR EACH ROW [trigger_order] trigger_body;  
	trigger_time: { BEFORE | AFTER }  
	trigger_event: { INSERT | UPDATE | DELETE }  
	trigger_order: { FOLLOWS | PRECEDES } other_trigger_name  
语法说明：  
	trigger_name：触发器名称，必须保持唯一；  
	tbl_name：表示建立触发器的表名，即在哪张表上建立触发器；  
	BEFORE | AFTER：表示触发动作的时间，即触发器将在每一行插入到表中之前还是之后激活；  
	INSERT | UPDATE | DELETE：表示触发事件，即激活触发器的操作类型；  
	FOR EACH ROW：用于定义触发器的主体，每次触发器激活时就会执行该语句，对于受触发事件影响的每一行都会执行一次；  
*/  
  
-- 创建测试表  
CREATE TABLE test (user_id INT, user_num DECIMAL(10, 2));  
  
-- 创建触发器  
CREATE TRIGGER test_trg BEFORE INSERT ON test FOR EACH ROW  
SET @sum = @sum + NEW.user_num;  
  
-- 创建多条语句的触发器  
DELIMITER # -- 使用 DELIMITER 命令来修改语句结束符  
CREATE TRIGGER test_trg BEFORE UPDATE ON test FOR EACH ROW  
BEGIN  
IF NEW.user_num < 1 THEN SET NEW.user_num = 1;  
ELSEIF NEW.user_num > 45 THEN SET NEW.user_num = 45;  
END IF;  
END;#  
DELIMITER ; -- 使用 DELIMITER 命令来修改语句结束符  
```


### 参考文档
1. [MySQL 创建触发器概述](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641920)，；
2. `创建 DML 触发器`：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220222)，[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641919)，；
3. `查看触发器信息`：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220220)，[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641917)，；
4. `删除触发器`：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220221)，[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641918)，；


