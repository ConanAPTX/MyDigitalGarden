---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/RPC 连接认证/","dgPassFrontmatter":true}
---


### RPC 连接认证
#### 1 功能简介
该功能旨在提升 RPC 通信的安全性；开启 RPC 连接认证后，只有通过认证的客户端才能与 OBServer 节点建立 RPC 连接，这一措施能够有效拦截攻击者的恶意 RPC 报文，防止其被 OBServer 节点执行，从而增强了 OceanBase 数据库的安全防护能力；

在升级兼容性方面，本方案支持灰度升级流程，可以分段平滑升级，不影响业务进程。在升级过程中，高版本与低版本的 OBServer 节点是相互兼容的。即高版本作为客户端能够连接低版本服务端，同时低版本作为客户端也能够连接高版本服务端。升级后，默认情况下不开启 RPC 连接认证，需要手动开启；

> [!NOTE] 限制条件：
> 1. OceanBase 数据库 V4.2.0 版本的 RPC 连接认证的功能，只有在启用 pkt-nio 网络框架后才能使用，即在开启 RPC 连接认证之前需要保证 `_enable_pkt_nio` 配置项设置为 True；
> 2. `_enable_pkt_nio` 用于控制 RPC 网络框架 pkt-nio 的启用和关闭，默认值为 True；


#### 2 使用示例
##### 2.1 开启认证
目前 OBServer 节点提供基于 SSL 握手的 RPC 连接认证，开启方式如下；

###### 2.1.1 准备 SSL 证书
需要在安装目录下创建 wallet 文件夹并将证书和私钥文件放入该文件夹；证书和私钥文件包括根证书（ca.pem）、证书（server-cert.pem）、私钥（server-key.pem）三个文件；

> [!NOTE] 说明：
> 1. 默认安装目录路径：/home/admin/oceanbase；
> 2. wallet 文件夹及文件夹下的文件名不能更改；
> 3. 若拥有多台服务器，则每台服务器的安装目录下都需创建 wallet 文件夹，并在该文件夹中放入证书和私钥；
> 4. 请确保所需的证书文件就绪后再开启认证，否则，会导致认证失败，进而服务不可用；

###### 2.1.2 执行开启命令
以 sys 租户身份登录 OceanBase 数据库，依次执行如下命令；

```sql
-- 1.选择 SSL 协议的版本号。目前支持的 SSL 协议的版本号包括 TLSv1、TLSv1.1、TLSv1.2 和 TLSv1.3，当指定版本号后，支持指定的版本号及其以上的版本协议；
ALTER SYSTEM SET sql_protocol_min_tls_version = 'TLSv1.1';

-- 2.开启 SSL 连接
ALTER SYSTEM SET ssl_client_authentication=True;

-- 3.将客户端的认证方式设置为 `SSL_NO_ENCRYPT`
ALTER SYSTEM SET rpc_client_authentication_method = 'SSL_NO_ENCRYPT';

-- 4.将服务端的认证方式设置为 `SSL_NO_ENCRYPT`
ALTER SYSTEM SET rpc_server_authentication_method = 'SSL_NO_ENCRYPT';
ALTER SYSTEM SET rpc_server_authentication_method = 'ALL';
	-- 虽然将服务端认证方式设置为 `ALL` 也可以开启认证，但这也意味着服务端允许客户端不进行认证直接连接，存在较大的安全风险。因此，在生产环境下不建议采用这种方式；
```

有关开启 SSL 连接的更多信息，请参见 [sql_protocol_min_tls_version](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220431)，请参见 [ssl_client_authentication](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220601)，；
有关客户端认证的详细信息，请参见 [rpc_client_authentication_method](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220448)，；
有关服务端认证的详细信息，请参见 [rpc_server_authentication_method](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220607)，；


##### 2.2 关闭认证
> [!NOTE] 注意事项：
> 如果后续需要更改认证方式，只需重新修改设置；
>  开启认证后，只对新建的 RPC 连接起作用，已有的连接不受影响。若想要对已连接的客户端进行认证，需要分批重启节点；

```sql
-- 1. 将客户端的认证方式设置为 NONE
ALTER SYSTEM SET rpc_client_authentication_method = 'NONE';

-- 2.  将服务端的认证方式设置为 NONE 或 ALL
ALTER SYSTEM SET rpc_server_authentication_method = 'NONE';
-- 或者
ALTER SYSTEM SET rpc_server_authentication_method = 'ALL';
```


### 参考文档
1. [RPC 连接认证_v4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218273)，；



