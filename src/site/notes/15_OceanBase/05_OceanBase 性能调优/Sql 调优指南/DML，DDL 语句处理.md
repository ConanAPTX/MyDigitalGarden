---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/Sql 调优指南/DML，DDL 语句处理/","dgPassFrontmatter":true}
---


### DML，DDL 语句处理
#### 1 DML 语句处理

数据操纵语言（Data Manipulation Language, DML）是SQL语言中，负责对数据库对象运行数据访问工作的指令集，以 *INSERT*、*UPDATE*、*DELETE* 三种指令为核心；

DML的主要功能即是访问数据，因此其语法都是以读取与写入数据库为主，除了 *INSERT* 以外，其他指令都可能需搭配 *WHERE* 指令来过滤数据范围，或是不加 *WHERE* 指令来访问全部的数据；

数据库还支持 *REPLACE* 和 *INSERT INTO...ON DUPLICATED KEY UPDATE* 两种 DML 语句；

所有的 DML 语句都会生成对应的执行计划来进行数据的读写操作，每一种 DML 语句都会生成一种对应的 DML 算子；DML 算子负责读取数据，并通过存储层提供的数据写入接口将 DML 语句涉及到的数据写入存储引擎中；

##### 1.1 INSERT执行计划示例

对于 *INSERT* / *REPLACE* 语句而言，由于其不用读取表中的已有数据，因此，*INSERT* 语句的执行计划相对简单，其执行计划为简单的 *EXPR VALUES + INSERT OP* 算子构成：

```sql
create table t1(a int primary key, b int, index idx1(b));

explain insert into t1 values(1, 1), (2, 2);
| ====================================
| ID | OPERATOR | NAME | EST. ROWS | COST |
------------------------------------
|0 |INSERT| |0 |0 |
|1 | EXPRESSION| |0 |0 |
====================================
Outputs & filters:
-------------------------------------
0 - output([column_conv(INT,PS:(11,0),NOT NULL,__values.a)],[column_conv(INT,PS:(11,0),NULL,__values.b)]), 
	filter(nil),columns([t1.a], [t1.b]), partitions(p0)
1 - output([__values.a], [__values.b]), filter(nil) values({1, 1}, {2, 2})
```


##### 1.2 UPDATE执行计划示例

对于 *UPDATE* 或者 *DELETE* 语句而言，优化器会通过代价模型对 *WHERE* 条件进行访问路径的选择，或者 *ORDER BY* 数据顺序的选择：

```sql
create table t1(a int primary key, b int, index idx1(b));

explain update t1 set b=10 where b=1;
| =======================================
|ID|OPERATOR |NAME |EST. ROWS|COST|
----------------------------------------
|0 |UPDATE | |1 |37 |
|1 | TABLE SCAN|t1(idx1)|1 |36 |
=======================================

Outputs & filters:
-------------------------------------
0 - output(nil), filter(nil), params([{t1: (t1.a, t1.b)}]), update([t1.b=?])
1 - output([t1.a], [t1.b], [?]), filter(nil),access([t1.b], [t1.a]), partitions(p0)
```


##### 1.3 DELETE执行计划示例

对于 *UPDATE* 或者 *DELETE* 语句而言，优化器会通过代价模型对 *WHERE* 条件进行访问路径的选择，或者 *ORDER BY* 数据顺序的选择：

```sql
create table t1(a int primary key, b int, index idx1(b));

explain delete from t1 where b=1;
| ========================================
|ID|OPERATOR |NAME |EST. ROWS|COST|
----------------------------------------
|0 |DELETE | |2 |39 |
|1 | TABLE SCAN|t1(idx1)|2 |37 |
========================================

Outputs & filters:
-------------------------------------
0 - output(nil), filter(nil), params([{t1: (t1.a, t1.b)}])
1 - output([t1.a], [t1.b]), filter(nil), access([t1.a], [t1.b]), partitions(p0)
```


##### 1.4 一致性校验

1. DML操作的表对象每一列都有相关的约束性定义，例如列的NOT NULL约束，UNIQUE KEY约束等。为保证写入数据的类型同 Schema 中列定义的类型保持一致，在 DML语句最终写入数据前需要进行如下操作：
	1. 对列的 NOT NULL、UNIQUE KEY 约束检查；
	2. 对写入数据进行类型转换；
2. 约束性检查失败，需要回滚该DML语句写入的脏数据；


##### 1.5 锁管理
1. 加锁时机 -通过MVCC和锁结合的机制；
	1. 只有行锁，没有表锁；在线DDL，不中断DML；
	2. 尽量避免大量DML语句对同一行进行频繁的并发读写；
	3. 热点行：可以使用SELECT...FOR UPDATE先对该行加锁，然后再执行DML操作；
2. 加锁顺序
	1. DML会级联的同步更新数据表中的数据和索引表中的数据；
	2. local index 和 单表 global index，绑定；
	3. 分区表global index，完全独立；


#### 2 DDL 语句处理
##### 2.1 DDL流程
OceanBase 数据库支持传统数据库的 DDL 语句，DDL 类型的 SQL 语句不会被优化器处理，而是作为 command 发送到 RootServer，由 RootServer 进行处理，比如典型的建表语句。在 OceanBase 数据库上的执行流程如下图所示：
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" height="206px" width="471px" viewBox="-10 -10 491 226" content="&lt;mxGraphModel dx=&quot;1394&quot; dy=&quot;649&quot; grid=&quot;1&quot; gridSize=&quot;10&quot; guides=&quot;1&quot; tooltips=&quot;1&quot; connect=&quot;1&quot; arrows=&quot;1&quot; fold=&quot;1&quot; page=&quot;0&quot; pageScale=&quot;1&quot; pageWidth=&quot;827&quot; pageHeight=&quot;1169&quot; math=&quot;0&quot; shadow=&quot;1&quot;&gt;&lt;root&gt;&lt;mxCell id=&quot;0&quot;/&gt;&lt;mxCell id=&quot;1&quot; parent=&quot;0&quot;/&gt;&lt;mxCell id=&quot;2&quot; value=&quot;Client&quot; style=&quot;rounded=1;whiteSpace=wrap;html=1;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;-40&quot; y=&quot;30&quot; width=&quot;80&quot; height=&quot;30&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;4&quot; value=&quot;RootServer&quot; style=&quot;rounded=1;whiteSpace=wrap;html=1;fillColor=#0050ef;strokeColor=#001DBC;fontColor=#ffffff;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;90&quot; y=&quot;120&quot; width=&quot;80&quot; height=&quot;30&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;6&quot; value=&quot;OBServer&quot; style=&quot;rounded=1;whiteSpace=wrap;html=1;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;220&quot; y=&quot;120&quot; width=&quot;80&quot; height=&quot;30&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;8&quot; value=&quot;OBServer&quot; style=&quot;rounded=1;whiteSpace=wrap;html=1;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;350&quot; y=&quot;120&quot; width=&quot;80&quot; height=&quot;30&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;9&quot; value=&quot;OBServer&quot; style=&quot;rounded=1;whiteSpace=wrap;html=1;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;-40&quot; y=&quot;120&quot; width=&quot;80&quot; height=&quot;30&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;11&quot; value=&quot;&quot; style=&quot;endArrow=classic;html=1;entryX=0.5;entryY=0;entryDx=0;entryDy=0;exitX=0.5;exitY=1;exitDx=0;exitDy=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; source=&quot;2&quot; target=&quot;9&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;310&quot; y=&quot;290&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;360&quot; y=&quot;240&quot; as=&quot;targetPoint&quot;/&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;12&quot; value=&quot;&quot; style=&quot;endArrow=classic;html=1;entryX=0;entryY=0.5;entryDx=0;entryDy=0;exitX=1;exitY=0.5;exitDx=0;exitDy=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; source=&quot;9&quot; target=&quot;4&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;310&quot; y=&quot;290&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;360&quot; y=&quot;240&quot; as=&quot;targetPoint&quot;/&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;20&quot; value=&quot;&quot; style=&quot;curved=1;endArrow=classic;html=1;entryX=0.5;entryY=1;entryDx=0;entryDy=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; target=&quot;6&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;122&quot; y=&quot;150&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;360&quot; y=&quot;240&quot; as=&quot;targetPoint&quot;/&gt;&lt;Array as=&quot;points&quot;&gt;&lt;mxPoint x=&quot;180&quot; y=&quot;170&quot;/&gt;&lt;mxPoint x=&quot;220&quot; y=&quot;170&quot;/&gt;&lt;/Array&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;21&quot; value=&quot;&quot; style=&quot;curved=1;endArrow=classic;html=1;entryX=0.5;entryY=1;entryDx=0;entryDy=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; target=&quot;8&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;120&quot; y=&quot;150&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;360&quot; y=&quot;240&quot; as=&quot;targetPoint&quot;/&gt;&lt;Array as=&quot;points&quot;&gt;&lt;mxPoint x=&quot;180&quot; y=&quot;190&quot;/&gt;&lt;mxPoint x=&quot;320&quot; y=&quot;180&quot;/&gt;&lt;/Array&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;22&quot; value=&quot;&quot; style=&quot;curved=1;endArrow=classic;html=1;entryX=0.5;entryY=1;entryDx=0;entryDy=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; target=&quot;9&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;120&quot; y=&quot;150&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;360&quot; y=&quot;240&quot; as=&quot;targetPoint&quot;/&gt;&lt;Array as=&quot;points&quot;&gt;&lt;mxPoint x=&quot;60&quot; y=&quot;190&quot;/&gt;&lt;/Array&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;25&quot; value=&quot;异步刷新&quot; style=&quot;text;html=1;resizable=0;autosize=1;align=center;verticalAlign=middle;points=[];fillColor=none;strokeColor=none;rounded=0;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;30&quot; y=&quot;170&quot; width=&quot;60&quot; height=&quot;20&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;27&quot; value=&quot;异步刷新&quot; style=&quot;text;html=1;resizable=0;autosize=1;align=center;verticalAlign=middle;points=[];fillColor=none;strokeColor=none;rounded=0;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;210&quot; y=&quot;190&quot; width=&quot;60&quot; height=&quot;20&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;28&quot; value=&quot;异步刷新&quot; style=&quot;text;html=1;resizable=0;autosize=1;align=center;verticalAlign=middle;points=[];fillColor=none;strokeColor=none;rounded=0;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;170&quot; y=&quot;150&quot; width=&quot;60&quot; height=&quot;20&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;29&quot; value=&quot;解析生成变更信息&quot; style=&quot;text;html=1;resizable=0;autosize=1;align=center;verticalAlign=middle;points=[];fillColor=none;strokeColor=none;rounded=0;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;60&quot; y=&quot;50&quot; width=&quot;110&quot; height=&quot;20&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;30&quot; value=&quot;&quot; style=&quot;endArrow=classic;html=1;exitX=0.75;exitY=0;exitDx=0;exitDy=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; source=&quot;9&quot; target=&quot;29&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;20&quot; y=&quot;100&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;360&quot; y=&quot;240&quot; as=&quot;targetPoint&quot;/&gt;&lt;Array as=&quot;points&quot;/&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;31&quot; value=&quot;1.变更信息持久化；&amp;lt;br&amp;gt;2.本地 schema 刷新&amp;lt;br&amp;gt;3.通知 observer 异步刷新&quot; style=&quot;text;html=1;resizable=0;autosize=1;align=left;verticalAlign=middle;points=[];fillColor=none;strokeColor=none;rounded=0;&quot; vertex=&quot;1&quot; parent=&quot;1&quot;&gt;&lt;mxGeometry x=&quot;230&quot; y=&quot;5&quot; width=&quot;150&quot; height=&quot;50&quot; as=&quot;geometry&quot;/&gt;&lt;/mxCell&gt;&lt;mxCell id=&quot;32&quot; value=&quot;&quot; style=&quot;endArrow=none;dashed=1;html=1;exitX=1;exitY=0;exitDx=0;exitDy=0;entryX=0.139;entryY=1.05;entryDx=0;entryDy=0;entryPerimeter=0;&quot; edge=&quot;1&quot; parent=&quot;1&quot; source=&quot;4&quot; target=&quot;31&quot;&gt;&lt;mxGeometry width=&quot;50&quot; height=&quot;50&quot; relative=&quot;1&quot; as=&quot;geometry&quot;&gt;&lt;mxPoint x=&quot;190&quot; y=&quot;280&quot; as=&quot;sourcePoint&quot;/&gt;&lt;mxPoint x=&quot;260&quot; y=&quot;60&quot; as=&quot;targetPoint&quot;/&gt;&lt;/mxGeometry&gt;&lt;/mxCell&gt;&lt;/root&gt;&lt;/mxGraphModel&gt;"><style type="text/css"></style><rect x="0.5" y="25.5" width="80" height="30" rx="4.5" ry="4.5" fill="#2a2a2a" stroke="#f0f0f0" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 78px; height: 1px; padding-top: 41px; margin-left: 2px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: normal; word-wrap: normal; ">Client</div></div></div></foreignObject></g><rect x="130.5" y="115.5" width="80" height="30" rx="4.5" ry="4.5" fill="#0050ef" stroke="#001dbc" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 78px; height: 1px; padding-top: 131px; margin-left: 132px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #ffffff; line-height: 1.2; pointer-events: none; white-space: normal; word-wrap: normal; ">RootServer</div></div></div></foreignObject></g><rect x="260.5" y="115.5" width="80" height="30" rx="4.5" ry="4.5" fill="#2a2a2a" stroke="#f0f0f0" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 78px; height: 1px; padding-top: 131px; margin-left: 262px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: normal; word-wrap: normal; ">OBServer</div></div></div></foreignObject></g><rect x="390.5" y="115.5" width="80" height="30" rx="4.5" ry="4.5" fill="#2a2a2a" stroke="#f0f0f0" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 78px; height: 1px; padding-top: 131px; margin-left: 392px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: normal; word-wrap: normal; ">OBServer</div></div></div></foreignObject></g><rect x="0.5" y="115.5" width="80" height="30" rx="4.5" ry="4.5" fill="#2a2a2a" stroke="#f0f0f0" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 78px; height: 1px; padding-top: 131px; margin-left: 2px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: normal; word-wrap: normal; ">OBServer</div></div></div></foreignObject></g><path d="M 40.5 55.5 L 40.5 109.13" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 40.5 114.38 L 37 107.38 L 40.5 109.13 L 44 107.38 Z" fill="#f0f0f0" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 80.5 130.5 L 124.13 130.5" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 129.38 130.5 L 122.38 134 L 124.13 130.5 L 122.38 127 Z" fill="#f0f0f0" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 162.5 145.5 Q 220.5 165.5 240.5 165.5 Q 260.5 165.5 294.8 148.35" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 299.5 146 L 294.8 152.26 L 294.8 148.35 L 291.67 146 Z" fill="#f0f0f0" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 160.5 145.5 Q 220.5 185.5 290.5 180.5 Q 360.5 175.5 424.65 148.01" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 429.47 145.94 L 424.42 151.91 L 424.65 148.01 L 421.66 145.48 Z" fill="#f0f0f0" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 160.5 145.5 Q 100.5 185.5 45.8 149.03" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 41.43 146.12 L 49.2 147.09 L 45.8 149.03 L 45.31 152.92 Z" fill="#f0f0f0" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 176px; margin-left: 101px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: nowrap; ">异步刷新</div></div></div></foreignObject></g><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 196px; margin-left: 281px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: nowrap; ">异步刷新</div></div></div></foreignObject></g><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 156px; margin-left: 241px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: nowrap; ">异步刷新</div></div></div></foreignObject></g><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe center; width: 1px; height: 1px; padding-top: 56px; margin-left: 156px;"><div style="box-sizing: border-box; font-size: 0; text-align: center; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: nowrap; ">解析生成变更信息</div></div></div></foreignObject></g><path d="M 60.5 115.5 L 134.28 68.9" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><path d="M 138.72 66.1 L 134.67 72.79 L 134.28 68.9 L 130.93 66.88 Z" fill="#f0f0f0" stroke="#f0f0f0" stroke-miterlimit="10" pointer-events="none"/><g><foreignObject style="overflow: visible; text-align: left;" pointer-events="none" width="100%" height="100%"><div xmlns="http://www.w3.org/1999/xhtml" style="display: flex; align-items: unsafe center; justify-content: unsafe flex-start; width: 1px; height: 1px; padding-top: 26px; margin-left: 273px;"><div style="box-sizing: border-box; font-size: 0; text-align: left; "><div style="display: inline-block; font-size: 12px; font-family: Helvetica; color: #f0f0f0; line-height: 1.2; pointer-events: none; white-space: nowrap; ">1.变更信息持久化；<br />2.本地 schema 刷新<br />3.通知 observer 异步刷新</div></div></div></foreignObject></g><path d="M 210.5 115.5 L 291.35 53" fill="none" stroke="#f0f0f0" stroke-miterlimit="10" stroke-dasharray="3 3" pointer-events="none"/></svg>

##### 2.2 DDL语句处理
OceanBase 支持传统数据库的 DDL 语句，自动完成全局统一的 schema 变更，无需用户在多节点间做 schema 一致性检查；DDL 任务由 OceanBase 的 RootServer 统一调度执行，保证全局范围内的 schema 一致性；

对于所有支持的 DDL 操作都是 OnLine 的，DDL 不会产生表锁，在执行 DDL 的过程中不会阻塞业务的读写操作；DML 根据 schema 信息的变更自动记录格式，对业务零影响；

DML 与 DDL 互相不阻塞，提高性能；


### 参考文档



