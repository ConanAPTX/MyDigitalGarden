---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/","dgPassFrontmatter":true}
---


### OceanBase 安全

#### 1 OceanBase 高可用
1. *V4.2.1:管理数据库 > 高可用 > 高可用概述*，OceeanBase 高可用；
2. V4.2.1:管理数据库 > 高可用 > 多副本容灾
3. V4.2.1:管理数据库 > 高可用 > 物理备库容灾
4. V4.2.1:管理数据库 > 高可用 > 仲裁高可用
5. *V4.2.1:管理数据库 > 高可用 > 回收站管理*
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase 闪回，回收站管理\|OceanBase 闪回，回收站管理]]，；
6. *V4.2.1:管理数据库 > 高可用 > 闪回查询*，`闪回`，；
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase Restore Point\|OceanBase Restore Point]]，；
	2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase  闪回查询多数据库版本\|OceanBase  闪回查询多数据库版本]]，；
	3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase 闪回查询\|OceanBase 闪回查询]]，；

#### 2 OceanBase 安全权限
1. *V4.2.1:管理数据库 > 安全权限 > 安全权限概述*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818464)，；
2. 身份鉴别和认证
	1. MySQL 模式下的身份鉴别和认证，；
	2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/Oracle 模式下的身份鉴别和认证\|Oracle 模式下的身份鉴别和认证]]，；
3. *V4.2.1:管理数据库 > 安全权限 > 访问控制* ，访问控制；
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/访问控制概述\|访问控制概述]]，；
	2. `用户和权限`，OceanBase 权限管理实战指南；
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/用户和权限概述\|用户和权限概述]]，；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 权限管理实战指南/OceanBase 数据库的权限分类\|OceanBase 数据库的权限分类]]，包括 MySql 租户，Oracle 租户；
		3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 权限管理实战指南/MySql 租户_用户和权限管理\|MySql 租户_用户和权限管理]]， `MySql 租户`；
		4.  [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 权限管理实战指南/Oracle 租户_角色管理\|Oracle 租户_角色管理]]，`Oracle 租户`；
		5. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 权限管理实战指南/Oracle 租户_用户和权限管理\|Oracle 租户_用户和权限管理]]，；
	3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 网络安全访问控制\|OceanBase 网络安全访问控制]]，详细介绍查看，设置租户白名单；
	4. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/RPC 连接认证\|RPC 连接认证]]，；
	5. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 行级访问权限控制\|OceanBase 行级访问权限控制]]，；
4. 数据传输加密
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/数据传输加密概述\|数据传输加密概述]]，；
	2. OBServer 传输加密
	3. ODP 传输加密
5. *V4.2.1:管理数据库 > 安全权限 > 数据透明加密*
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/数据库透明加密概述\|数据库透明加密概述]]，；
	2. MySQL 模式下的数据透明加密
	3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/Oracle 模式下的数据透明加密\|Oracle 模式下的数据透明加密]]，；
6. *V4.2.1:管理数据库 > 安全权限 > 安全审计*
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/安全审计概述\|安全审计概述]]，；


#### 3 OceanBase 备份恢复
1. *V3.2.3:使用指南 > 高可用和容灾  > 备份恢复管理*
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 物理备份与恢复概述\|OceanBase 物理备份与恢复概述]]，；
	2. 部署 NFS
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OB 备份前准备\|OB 备份前准备]]，；
			1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/backup_dest_option 配置项\|backup_dest_option 配置项]]，；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/计算 OceanBase 备份存储空间大小\|计算 OceanBase 备份存储空间大小]]，；
		3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/部署 NFS\|部署 NFS]]，；
	3. 备份数据
		1. 日志归档
			1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/日志归档概述_V4.x\|日志归档概述_V4.x]]，；
			2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OB 发起日志备份\|OB 发起日志备份]]，日志备份的启动，停止，及查询日志备份的状态；
			3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/开启基于日志分片的备份_集群级别\|开启基于日志分片的备份_集群级别]]，；
			4. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/更改归档目的端\|更改归档目的端]]，；
			5. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/查询 OceanBase 备份信息_V2.x，V3.x\|查询 OceanBase 备份信息_V2.x，V3.x]]，包括*数据备份*，*日志备份*；
		2. 数据备份
			1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OB 发起数据备份\|OB 发起数据备份]]，数据备份的启动，停止，及查询备份状态，包括全量备份和增量备份；
		3. 清理备份数据
			1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/清理备份的数据\|清理备份的数据]]，介绍备份数据的清理，包括自动，手动清理备份数据，及停止清理备份数据；
	4. 备份备份的数据
	5. 备份过程中更新备份目的端中 OSS 的密钥信息
	6. 数据校验
	7. `恢复数据`
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行全量恢复\|OceanBase 执行全量恢复]]，包括：【全量恢复租户】，【取消恢复】；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 恢复指定的表\|OceanBase 恢复指定的表]]，；
		3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行备份备份数据的恢复\|OceanBase 执行备份备份数据的恢复]]，；
			1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行备份备份数据的恢复_实践\|OceanBase 执行备份备份数据的恢复_实践]]，；
		4. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行物理恢复\|OceanBase 执行物理恢复]]，；
		5. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/查看 OceanBase 恢复进度和结果_V4.x\|查看 OceanBase 恢复进度和结果_V4.x]]，；
	8. 日常维护
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/查看备份相关参数\|查看备份相关参数]]，查看备份相关参数；
		2. 强制停止备份相关的所有任务
		3. 设置 backup_zone
2. *V4.2.1:管理数据库 > 备份恢复 * ：
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 物理备份与恢复概述_V4.x\|OceanBase 物理备份与恢复概述_V4.x]]，；
	2. 极简化体验物理备份与恢复
	3. 部署 NFS
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OB 备份前准备\|OB 备份前准备]]，；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/计算 OceanBase 备份存储空间大小\|计算 OceanBase 备份存储空间大小]]，；
		3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/部署 NFS\|部署 NFS]]，；
	4. `日志归档`
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/日志归档概述_V4.x\|日志归档概述_V4.x]]，；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 日志归档的准备工作_V4.2.1\|OceanBase 日志归档的准备工作_V4.2.1]]，；
		3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 开启，暂停，关闭归档模式_V4.2.1\|OceanBase 开启，暂停，关闭归档模式_V4.2.1]]，；
		4. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/查询 OceanBase 日志归档信息_V4.x\|查询 OceanBase 日志归档信息_V4.x]]，；
		5. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/更改归档目的端\|更改归档目的端]]，；
	5. 数据备份
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 数据备份备份前准备_V4.2.1\|OceanBase 数据备份备份前准备_V4.2.1]]，；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/OceanBase 发起数据备份_V4.x\|OceanBase 发起数据备份_V4.x]]，；
	6. 清理备份数据
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/清理备份的数据\|清理备份的数据]]，介绍备份数据的清理，包括自动，手动清理备份数据，及停止清理备份数据；
	7. 恢复数据
		1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行全量恢复\|OceanBase 执行全量恢复]]，；
		2. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行备份备份数据的恢复\|OceanBase 执行备份备份数据的恢复]]，；
		3. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理/OceanBase 执行物理恢复\|OceanBase 执行物理恢复]]，；
		4. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/备份恢复管理_V4.x/查看 OceanBase 恢复进度和结果_V4.x\|查看 OceanBase 恢复进度和结果_V4.x]]，；
	8. 按表恢复


### 参考文档

7. *V4.2.1:管理数据库 > 备份恢复 > 物理备份与恢复概述*
8. V4.2.1:管理数据库 > 备份恢复 > 极简化体验物理备份与恢复
9. V4.2.1:管理数据库 > 备份恢复 > 
10. *V4.2.1:管理数据库 > 备份恢复 > 日志归档*
11. V4.2.1:管理数据库 > 备份恢复 > 数据备份
12. *V4.2.1:管理数据库 > 备份恢复 > 清理备份数据*
13. *V4.2.1:管理数据库 > 备份恢复 > 恢复数据*
14. V4.2.1:管理数据库 > 备份恢复 > 按表恢复
15. 管理数据库 > 安全权限
16. 管理数据库 > 备份恢复，`OceanBase 数据库备份恢复管理`
17. 主备库管理
	1. [[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/新建备集群\|新建备集群]]，通过 OCP 新建 OB 备集群；