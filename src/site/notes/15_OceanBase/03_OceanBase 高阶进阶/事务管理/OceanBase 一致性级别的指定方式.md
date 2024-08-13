---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 一致性级别的指定方式/","dgPassFrontmatter":true}
---


### 文档名称

#### 1 一致性级别的指定方式
在 OB 数据库中有 2 种方式指定一致性级别：*WEAK(弱读)*，*STRONG*；

> [!NOTE] SQL 语句的一致性级别：
> 1. 写语句 DML (INSERT/DELETE/UPDATE)：强制使用 *STRONG Consistency*，要求基于最新数据进行修改；
> 2. `SELECT FOR UPDATE`（SFU）：与写语句类似，强制使用 *STRONG Consistency*；
> 3. 只读语句 `SELECT`：用户可以配置不同的 *Consistency Level*，满足不同的读取需求；
> 4. 一致性级别优先级：*sql Hint* > *Session* > *Global*，即若语句中加了 Hint，会覆盖 Session ；

##### 1.1 通过设置系统变量
通过 `ob_read_consistency` 系统变量指定；

```sql
-- 设置 Session 变量，影响当前 Session
SHOW SESSION VARIABLES LIKE 'ob_read_consistency'; 
obclient> SET ob_read_consistency = WEAK;
obclient> SELECT * FROM t1;  -- 弱一致性读

-- 设置 Global 变量，影响之后新建的所有 Session
SHOW GLOBAL VARIABLES LIKE 'ob_read_consistency';
obclient> SET GLOBAL ob_read_consistency = STRONG;
	-- 对当前会话不生效；
```

##### 1.2 指定 Hint 方式
指定 `READ_CONSISTENCY` 的方式优先级高于 `ob_read_consistency`；

```sql
-- 通过指定 Hint 方式设置一致性级别
SELECT /*+READ_CONSISTENCY(WEAK) */ * FROM t1;   -- 指定 WEAK Consistency
SELECT /*+READ_CONSISTENCY(STRONG) */ * FROM t1; -- 指定 STRONG Consistency
```


#### 2 事务的一致性级别

弱一致性读的最佳实践是为不在事务中的 SELECT 语句指定 WEAK 一致性级别，它的语义是确定的。对于显式开启事务的场景，语法上，OceanBase 数据库允许不同的语句配置不同的一致性级别，这样会让用户很困惑，而且如果使用不当，SQL 会报错；

> [!NOTE] 原则：
> 1. 一致性级别是事务级的，事务内所有语句采用相同的一致性级别；
> 2. 由事务的第一条语句决定事务的一致性级别，后续的 SELECT 语句如果指定了不同的一致性级别，则强制改写为事务的一致性级别；
> 3. 写语句和 SFU 语句只能采用 STRONG，如果事务级一致性级别为 WEAK，则报错 `OB_NOT_SUPPORTED`；


> [!NOTE] 对于单条 SQL 而言，一致性级别的确定规则优先级从大到小可以概括为：
> 1.  根据语句类型确定的一致性级别，例如 DML 和 SFU 必须采用 *STRONG*；
> 2.  事务的一致性级别，如果语句在事务中，而且不是第一条语句，则采用事务的一致性级别；
> 3.  通过 Hint 指定的一致性级别；
> 4.  系统变量指定的一致性级别；
> 5.  缺省采用 *STRONG*；


```sql
BEGIN;
insert into t1 values (1);   -- 修改语句，consistency_level=STRONG，整个事务应该是 STRONG             

-- SQL自身的 consistency_level=WEAK，但由于第一条语句为 STRONG，因此这条语句的 consistency_level 强制设置为 STRONG
select /*+READ_CONSISTENCY(WEAK) */ * from t1;
COMMIT;

BEGIN;
select * from t1 for update;   -- SFU属于修改语句，consistency_level=STRONG，整个事务应该也是STRONG

-- SQL自身的consistency_level=WEAK，但由于第一条语句为STRONG，因此这条语句的consistency_level强制设置为STRONG
select /*+READ_CONSISTENCY(WEAK) */ * from t1;
COMMIT;

BEGIN;
select /*+READ_CONSISTENCY(WEAK) */ * from t1;  -- 第一条语句为WEAK
select * from t1;  -- 虽然本条语句为STRONG，但是会继承第一条语句的consistency level，会强制设置为WEAK
COMMIT;

BEGIN;
select /*+READ_CONSISTENCY(WEAK) */ * from t1;  -- 第一条语句为WEAK
insert into t1 values (1);  -- 修改语句，必须为STRONG，由于第一条语句为WEAK，这里会报错：NOT SUPPORTED
select * from t1 for update;  -- SFU属于修改语句，必须为STRONG，这里同样会报错：NOT SUPPORTED
COMMIT;
```


> [!NOTE] `与隔离级别关系：`
> 1. STRONG 支持所有的隔离级别；
> 2. WEAK 仅支持读已提交 `READ COMMITTED` 隔离级别，其他隔离级别下会报错 `OB_NOT_SUPPORTED`；



### 参考文档

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



### 参考文档
1. *弱一致性读*：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355270)，；





</div></div>




