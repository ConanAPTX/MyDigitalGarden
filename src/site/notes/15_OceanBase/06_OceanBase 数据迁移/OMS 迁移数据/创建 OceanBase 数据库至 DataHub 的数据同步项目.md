---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/创建 OceanBase 数据库至 DataHub 的数据同步项目/","dgPassFrontmatter":true}
---


### 创建 OceanBase 数据库至 DataHub 的数据同步项目
#### 1 前提条件

#### 2 使用限制及注意事项
*V3.2.2 版本，其他版本请查看官方文档；*
> [!NOTE] `使用限制`：
> 1. 选择全量同步时，OMS 仅支持同步具有唯一键的表，不支持同步无主键表；
> 2. 增量同步时，不支持同步没有分片列的无主键表；
> 3. 同步 OceanBase 数据库的增量数据至 DataHub 时，其中包含表结构至 DataHub Schema 的初始化 Schema；
> 4. 当一条链路意外中断进行断点续传时，DataHub 内可能会存在部分重复数据（最近一分钟内），因此下游系统需要具备排重能力；
> 5. OMS 支持字符集配置为 UTF8 和 GBK 时的数据同步；
> 6. 待同步的表名和其中的列名不能包含中文字符；

> [!NOTE] `DataHub 相关的使用限制如下`：
> 1. DataHub 官方限制一条消息具体的大小根据云环境而定，通常为 1 MB；
> 2. 由于是攒批发送，DataHub 限制一次只能发送 4 MB 的消息。如果单条消息满足可发送的前提，您可以通过更改 Connector Sink 端的配置项 `batch.size` 来设置攒批大小，默认为 20 条消息攒批发送（时间窗口一秒内）；
> 3. DataHub 的官方限制和命名规范请参见 [DataHub 的限制描述](https://help.aliyun.com/document_detail/47441.html)，；

> [!NOTE] `同步 DDL 支持的范围：`
> 1. 支持同步单物理表至 DataHub 单 Topic；
> 2. 支持同步 OceanBase 数据库 MySQL 租户的逻辑表至 DataHub；
> 3. 支持增量 DML 同步：`INSERT`、`UPDATE` 和 `DELETE`；


#### 3 数据类型映射
*OceanBase → Datahub 数据类型映射*：[[15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OceanBase → Datahub 数据类型映射\|OceanBase → Datahub 数据类型映射]]，；


#### 4 补充 Properties
如果自行创建 Topic，启动数据同步项目前，请在 DataHub 的 Schema 中补充下述 Properties。如果由 OMS 自动创建 Topic 和结构同步，OMS 会自动补充下述 Properties。

| 名称 | 类型 | 含义 |
| :------------- | :---------- | :------------ |
| oms_timestamp | STRING | 发生变更的时间。 |
| oms_table_name | STRING | 源端为表时，变更表名。 |
| oms_database_name | STRING | 源端为数据库时，变更库名。 |
| oms_sequence | STRING | 数据至同步进程内存中的时间戳，由时间 + 5 位递增数字组成。如果发生时钟回退的情况，会导致数据不一致；<br>`注意`：若变更类型为 *UPDATE*，修改 1 条数据，在 *datahub* 中会有 2 条数据，且该值相同； |
| oms_record_type | STRING | 变更类型，包括 UPDATE、INSERT 和 DELETE。 |
| oms_is_before | STRING | 如果是 UPDATE 类型，Y 表示该条数据为变更前的数据。 |
| oms_is_after | STRING | 如果是 UPDATE 类型，Y 表示该条数据为变更后的数据； |

#### 5 操作步骤
建议参考官方文档；

### 参考文档
1. *创建 OceanBase 数据库至 DataHub 的数据同步项目*：[V3.2.2](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-10000000000373409)，[V4.2.2](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613335)，；


