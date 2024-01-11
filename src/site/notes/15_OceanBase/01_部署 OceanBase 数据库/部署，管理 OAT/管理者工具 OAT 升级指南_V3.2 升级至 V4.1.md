---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/管理者工具 OAT 升级指南_V3.2 升级至 V4.1/","dgPassFrontmatter":true}
---


### 管理者工具 OAT 升级指南_V3.2 升级至 V4.1

> [!NOTE] 说明：
> 1. 本文档仅适用于从 OAT V3.2 或 V4.0 升级至 V4.1。V3.2 以下的版本不支持升级，仅支持接管；
> 2. 本文档将以 OAT V3.2 为例，介绍升级流程；

#### 1 操作步骤

按照以下步骤，将 OAT V3.2 升级至 V4.1；

1.  加载新版本镜像；
```shell
docker load -i <新版本镜像> 
```


2.  关闭容器内的 MariaDB；
```shell
docker exec -it <容器名> bash   # 进入容器
pkill mysqld                   # 终止运行中的 MySQL 服务进程
pgrep mysqld                   # 检查 MySQL 服务是否正在运行，确保没有运行中的 MySQL 进程
exit           # 返回宿主机
```


3.  查看 OAT V3.2 容器启动时指定的宿主机上的数据挂载目录；
```shell
docker inspect <容器名> -f '{{.Mounts}}'
	# 返回以下结果：[{bind  /data_dir /data   true rprivate}]
```

宿主机上的 `/data_dir` 对应容器内的 `/data` 目录。在步骤 5 启动 OAT V4.1 时，`/data_dir` 务必保持一致；


4.  停止并删除 OAT V3.2 容器；
```shell
docker stop <容器名> && docker rm <容器名>
```


5.  使用数据挂载目录启动 OAT V4.1 容器；
```shell
docker run -d --name oat --net host -v <data_dir>:/data --restart on-failure:5  reg.docker.alibaba-inc.com/oceanbase/oat:4.1.0_20230331_x86
<<xxxx # 多行注释
	
xxxx
```


6.  查看启动日志；
```shell
docker logs -f oat
```

启动日志停止刷新后，登录 OAT 验证升级是否成功；


### 参考文档
1. [管理者工具 OAT 升级指南_V3.2 升级至 V4.1](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-10000000001755763)，；


