---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/部署，管理 OBProxy/OBProxy 部署/","dgPassFrontmatter":true}
---


### 文档名称
#### 1 OBProxy 运行环境:
 OS: Linux Redhat 7u x86-64 及以上
 OS内核：3.10 及以上版本
 CPU: 2 核及以上
 内存：1G 及以上
 磁盘空间：对磁盘大小没有特别需求，推荐10G 及以上，主要用于存放OBProxy的应用日志;

#### 2 OBProxy 部署方式

部署方式和真实的物理环境、业务需求等相关，生产环境中部署 ODP 主要考虑以下三个因素：*交付方式*，*数量*，*部署方式*；

`交付方式`：ODP 提供 RPM 包的部署形式，支持通过 OBD、OCP 和命令行进行部署；`数量`：我们对 ODP 的数量没有做任何限制，但真实部署时，ODP 的数量和 APP 或 OBServer 节点的数量有一定关系，这取决于具体的部署方式；`部署方式`：不同部署方式背后反映的是 RT 和资源抢占，有 OBServer 端部署和独立部署两种方式，部署方式的不同极大地影响着性能；

OBProxy 部署方式：*部署在 OBServer 端*，*独立部署*，*客户端部署*；
 
##### 2.1 部署在 OBServer 端
*部署在 OBServer 端* 是指在部署 OceanBase 数据库的机器上部署一个 ODP 进程，这样 OBServer 节点和 ODP 的数量满足 1:1 关系。此种方式下，ODP 数量和 APP 没有了对应关系。除了一个节点部署一个 ODP 这种情况外，也可以一个 Zone 内部署一个 ODP；

##### 2.2 独立部署
*独立部署* 是指专门为 ODP 找一台机器部署。此时 ODP 的数量和 APP、OBServer 节点都没有关系，根据具体业务需求确定 ODP 的数量；

对于 ODP 的部署机型，一般推荐选择小机型即可，如云上使用 16C16G 的 ECS。独立部署后，OceanBase 数据库和 ODP 之间不存在资源抢占，可以更好地管理 ODP，将 ODP 做成资源池对外服务，目前公有云使用了该部署方式；

##### 2.3 客户端部署
*客户端部署* 即将 OBproxy 与应用程序部署在一起；



#### 3 123

`Region`：地域信息，通常代表一个城市，包含一个或多个 IDC，每个 IDC 部署一个或多个 zone；一个 OB 集群可以包含若干个 Region，每个 Region 包含若干个 IDC，每个 IDC 部署若干个 zone；

`IDC` : 互联网数据中心，可以简单看成一个物理机房；

`LDC` ：logical data center ，是对 IDC 的一种逻辑划分；

##### 3.1 LDC 配置：集群的配置
LDC 是指 obproxy 按照根据城市，机房等信息就近路由访问 observer，这需要 observer 设置好自身所处的机房和城市信息，需要 proxy 提供自身所处的机房信息；
 
```sql
-- 配置 OB 集群的 LDC 配置
alter system modify zone 'z1' set region = "SHANGHAI";
alter system modify zone 'z1' set idc = "zue";

-- 检查 observer LDC 设置内容是否生效
select * from __all_zone;
```


##### 3.2 LDC 配置：obproxy 的配置
obproxy 支持客户端，集中式部署，因此 obproxy 的 LDC 的支持 `全局级别` ，`session 级别`，即可以全局设置默认的 LDC，也可以每个 session 可以指定使用不同的 LDC；

配置项 `proxy_idc_name` 用来控制全局级别的当前 IDC 机房信息, 默认为空，配置项的设置可以登陆 OB 集群通过 SQL 语句修改；

```sql
-- 通过 obproxy 连接上 ob 集群 

-- 检查 proxy 内部识别的 LDC 部署情况
show proxyinfo idc;

-- 全局级别：
	-- 修改proxy配置项：修改 obproxy 的 LDC 配置
	alter proxyconfig set proxy_idc_name = '机房名';
	alter proxyconfig set proxy_idc_name = 'hz001';

--  session 级别：
	-- 设置用户变量
	set @proxy_idc_name = 'xx'    --  控制 session 级别的当前机房信息, 默认不指定；
		-- 当用户指定 session 变量 proxy_idc_name 时，覆盖全局 proxy_idc_name 设置；
```

除此之外还可以通过以下方式修改：*在 proxy 的启动脚本中使用 -i 机房名启动传入*，*在 OCP 上修改 obproxy 配置项* ；

```shell
# 首次启动时通过启动参数(推荐)：
./bin/obproxy -p2883 -e -o obproxy_config_server_url='ocp_config_server_url',proxy_idc_name='hz001' -n trade
```

obproxy 的 LDC 配置 *session 变量*的优先级大于*配置项*，即：`当用户指定 session 变量 proxy_idc_name 时，覆盖全局 proxy_idc_name 设置`；

### 参考文档
1. *OBproxy 部署方式*：[V4.3.0](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000755425)，；




 

 




