---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 日志排查/","dgPassFrontmatter":true}
---



### 文档名称
OMS 日志的默认目录为 `/home/admin/logs/`；本文为您介绍 OMS 各组件的日志；已官方文档 v4.2.3 版本为模版；

> [!NOTE] `说明`：
> 如果需要查看 OMS 的日志，则必须登录至部署 OMS 的机器，然后进入 OMS 的容器；执行以下命令，进入部署 OMS 的容器：`docker exec -it <oms_container_name> bash`


#### 1 Console 组件日志
Console 组件日志的默认目录为 `/home/admin/logs/ghana/Ghana`；Console 组件日志分类见下表：

|日志类型|描述|
|---|---|
|`common-default.log`|`INFO`、`WARNING` 和 `ERROR` 级别日志。|
|`common-error.log`|`ERROR` 级别日志。如果 OMS 报错，在此日志搜索报错时间，定位报错原因。|
|`oms-scheduler.log`|后台调度日志。|
|`oms-step`|迁移同步项目步骤执行日志。|
|`oms-api.log`|外部 API 调用日志。|
|`database.log`|数据库调用日志。|
|`oms-web.log`|外部访问 Console 日志，主要包括 URL、请求路径、执行耗时和接口响应结果。|
|`oms-alarm.log`|告警相关日志。|
|`dbcat.log`|结构迁移组件 DBCat 的日志。 SQL 查询报错时，在此日志中筛选 `ERROR` 关键字即可获得对应错误原因。|


#### 2 CM 组件日志
CM 组件日志的默认目录为 `/home/admin/logs/cm/log`；CM 组件日志分类见下表：

|日志类型|描述|
|---|---|
|`service.log`|CM 组件的服务日志。此日志包含报错原因和堆栈信息。排查问题优先查看此日志。|
|`cm-web.log`|CM 组件的网络日志，包含 OMS Console 和 Agent 等组件对 CM 组件的接口、请求参数、结果和时间等。|
|`dao-digest.log`|数据访问对象日志。每行日志表示对 OMS 元数据库执行的一个 DML 操作。最后一位数字表示 DML 操作的执行时间。如是执行时大于 60ms，则表示元数据库可能存在异常。|
|`logs.log`|日常 Controller 日志。|

#### 3 Store 组件日志
##### 3.1 store 组件日志目录
Store 组件日志的默认目录为 `/home/ds/store/store{storeport}/log`；

```shell
## 获取 store 组件日志(增量拉取组件)
	## /home/ds/store/store{storeport}/log  
		# storeport：组件 ID 中的端口；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/store/store7131/log  
		# 100.63.40.216-7131:ob_ten_1_5ukd9qzmhk3k-1-0:0001000066
```

##### 3.2 日志详细介绍
1. `store.log`：Store 组件的标准输出和标准错误输出会重定向到该日志。如果您在其他日志文件中找不到有效错误信息，可以检查此日志；
2. `congo.log`：此日志记录启动 Store 上的 Reader（事务日志抓取任务）时的相关的配置项；
	1. MySQL Reader 将 Binlog 拉取和解析相关的日志也记录在此日志中。定位 MySQL Reader 的问题时，您可以在此日志文件中搜索 ERROR 日志；如果找不到有效信息，您可以从数据目录下的 meta.log 文件中查找；
	2. Store 组件的数据目录名由配置文件 conf/crawler.conf 中的 subTopic 和 subId 两个配置项组成，格式为 `${subTopic}.${subId}`；
3. `liboblog.log / libobcdc.log`： 此日志记录 OceanBase Reader 启动后的拉取和解析相关的日志，定位 OceanBase Reader 问题时查看此日志；
	2. OceanBase Store V2.x 和 V3.x 的日志名称为 *liboblog.log*，V4.x 的日志名称为 *libobcdc.log*；
4. `connector.log`：此日志记录 Oracle、Db2 LUW 和 Postgresql 的 Reader 在启动后的拉取和解析相关的日志；定位以上数据源的 Reader 相关问题时查看此日志；


#### 4 Full-Import / Incr-Sync 组件日志
##### 4.1 Full-Import / Incr-Sync 组件日志目录 
OMS 的 Full-Import(全量导入组件) / Incr-Sync(增量导入组件) 组件日志的默认目录为 `/home/ds/run/{name}/logs`；

```shell
## 获取 Full-Import(全量导入组件) / Incr-Sync(增量导入组件) 组件日志
	# cd /home/ds/run/{name}/logs 
		# name：组件 ID；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs  
		# 组件 ID：100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007
```


##### 4.2 日志详细介绍
Full-Import / Incr-Sync 组件日志分类见下表：

|日志类型|描述|
|---|---|
|`链路根目录下链路标识.out`|项目启动日志。|
|`logs/connector.log`|运行时 info 级别的日志。|
|`logs/error.log`|运行时 error 级别的日志，项目中的错误日志。|
|`logs/trace.log`|针对 RecordBatch 进行的抽样采集的 trace 日志。|
|`logs/msg/connector_filter_msg.log`|ETL 过滤消息的日志。|
|`logs/msg/connector_sink_msg.log`|成功写入目标端消息日志。|
|`logs/msg/connector_source_msg.log`|成功读取到源端消息日志。|
|`logs/msg/ddl_msg.log`|DDL 相关日志，会打印全部 DDL。|
|`logs/msg/metrics.log`|metrics 指标日志。|
|`logs/msg/sql_msg.log`|慢执行的 DML 语句（执行时间超过 15ms）。|
|`logs/msg/manual_table.log`|增加特殊列和索引的表记录。这些表进行反向迁移前，您需要手动删除对应的特殊列和索引。|

#### 5 Full-Verification 组件日志

##### 5.1 Full-Verification 组件日志目录 
Full-Verification(全量校验组件) 组件日志的目录为 `/home/ds/run/{name}/logs`；

```shell
##  获取 Full-Verification(全量校验组件) 组件日志
	# cd /home/ds/run/{name}/logs
		# name：组件 ID；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/run/192.10.111.111:90218:0000000004/logs
```



##### 5.2 日志详细介绍
Full-Verification 组件日志分类见下表：

|日志类型|描述|
|---|---|
|`error.log`|项目的错误日志|
|`metrics.log`|项目的指标监控|
|`task.log`|项目的运行日志|

重要文件日志：

> [!NOTE] 说明：
> 下述日志路径中的 `{subid}` 对应 OMS 自动传入的 `task.subId` 配置；

- `/home/ds/run/{taskname}/[migrate|verify]/{subid}/heartbeat`
- `/home/ds/run/{taskname}/[migrate|verify]/{subid}/overview-running`
- `/home/ds/run/{taskname}/[migrate|verify]/{subid}/overview-done`
- `/home/ds/run/{taskname}/[migrate|verify]/{subid}/overview-failed`

校验结果文件：
`/home/ds/run/{taskname}/[migrate|verify]/{subid}/{schema}/diff/{tablename}.diff`

订正 SQL 文件：
`/home/ds/run/{taskname}/[migrate|verify]/{subid}/{schema}/sql/{tablename}.sql`



#### 6 Supervisor 组件日志
请根据 `/home/ds/supervisor/config/drc.properties` 文件中 `logging.path` 项找到 Supervisor 组件日志所在位置；OMS 企业版环境中，通常 Supervisor 组件日志的目录为 `/home/admin/logs/supervisor/`；

您只需要关注以下日志文件：

|日志类型|描述|
|---|---|
|`error.log`|打印所有 ERROR 级别的日志。|
|`legacy.log`|记录 OMS 控制台和 Agent 等组件对 CM 组件的接口、请求参数、结果和时间等。|
|`routine.log`|打印 Supervisor 组件中，各种定时调度任务相关的日志。<br><br>- 心跳采集汇报相关日志。<br>- 指标、资源、错误采集汇报相关日志。<br>- Supervisor 组件后台任务相关日志。|
|`supervisor.log`|打印全部 Supervisor 日志。|


### 参考文档
1. *OMS 日志排查*：[V4.2.3](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000987922)，；


