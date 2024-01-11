---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/生产环境运行 OBProxy - 守护进程/","dgPassFrontmatter":true}
---


### 文档名称
#### 1 

OBProxy 无状态，即使宕机重启也不会影响数据一致性，所以 OBProxy 在部署时都带有一个守护进程，周期性检查 OBProxy 的健康程度，一旦发现宕机就立即重启 OBProxy；

OBProxy 手动启动和检查过程如下：

```shell
 登录 OBProxy 所在的宿主机，使用 admin 用户在命令行工具中运行
 /opt/taobao/install/<obproxy目录>/bin/obproxyd.sh -c start -e private -n <obproxy名称>
  -- 以启动守护进程，守护进程会自动拉起 OBProxy 进程
 
 # 检查 OBProxy 进程状态：
 ps -ef | grep obproxy | grep '^admin'
```



```sql
 -- 在 OceanBase 数据库支持的客户端工具中使用 root 用户登录集群的 sys 租户
 obclient - uroot@sys#[集群名称] -P2883 -h127.0.0.1 -p[密码] -Doceanbase -c
	 -- 以验证是否能连到 OceanBase 集群
```

### 参考文档

