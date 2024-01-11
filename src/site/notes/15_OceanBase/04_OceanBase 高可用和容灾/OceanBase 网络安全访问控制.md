---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/OceanBase 网络安全访问控制/","dgPassFrontmatter":true}
---


### OceanBase 网络安全访问控制
OceanBase 数据库提供白名单策略，实现网络安全访问控制；可以通过租户白名单功能来设置允许哪些客户端访问当前租户；

租户白名单通过系统变量 `ob_tcp_invited_nodes` 控制，是租户全局的白名单限制参数；默认值为 `127.0.0.1,::1`，表示仅允许本机的 IP 连接该租户。 该变量支持列表形式取值，列表值之间使用英文逗号（,）分隔，例如：A,B,C,D；用户登录时，OBServer 会将用户的 IP 地址依次和 A、B、C、D 进行匹配校验。如果全部不匹配，则拒绝访问。只要有任意一个匹配成功，则表示通过白名单；

> [!NOTE] `系统支持以下多种租户白名单格式：`
> 1. IP 地址的形式；示例：10.10.10.10,  10.10.10.11
> 2. 子网/掩码的形式；示例：10.10.10.0/24
> 3. 模糊匹配的形式；示例：10.10.10.% 或者 10.10.10._
> 4. 多种格式混合的形式；示例：10.10.10.10,  10.10.10.11,  10.10.10.%,  10.10.10.\_,  10.10.10.0/24


#### 1 通过 SQL 语句查看和设置租户白名单
系统变量 `ob_tcp_invited_nodes` 用于设置租户的 IP 白名单，是租户全局的白名单限制参数；默认值为 127.0.0.1,::1，表示仅允许本机的 IP 连接该租户；

```sql
-- 租户管理员登录数据库

-- 查看租户的白名单
obclient> SHOW VARIABLES LIKE 'ob_tcp_invited_nodes';
+----------------------+-------+
| Variable_name        | Value |
+----------------------+-------+
| ob_tcp_invited_nodes | %     |
+----------------------+-------+
-- 其中，值为 % 表示允许任何客户端 IP 连接该租户；

-- 重新设置租户的白名单
obclient> SET GLOBAL ob_tcp_invited_nodes='%';
obclient> SET GLOBAL ob_tcp_invited_nodes='10.10.10.%';
```

#### 2 通过 OCP 查看和设置租户白名单



### 参考文档
1. [网络安全访问控制_v4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218274)，；
