 # zookeeper-ACL权限控制
 
 
ZooKeeper 中的 ACL（访问控制列表）机制，是用来控制 **对 ZNode 节点的访问权限** 的安全机制。每个 ZNode 都可以设置一个 ACL 列表，来指定谁可以对该节点进行什么操作。

ACL 本质上是一个 **“授权信息”列表**，每个 ACL 包含两个部分：
- **scheme（认证方案）**
- **id（用户身份）**
- **权限（权限位）**

---

## 1.权限类型（Permissions）

ZooKeeper 提供 5 种权限（以位标志方式组合）：

| 权限 | 含义 | 说明 |
|------|------|------|
| `READ` (r) | 读取 | 读取节点数据和子节点 |
| `WRITE` (w) | 写入 | 修改节点数据 |
| `CREATE` (c) | 创建 | 在此节点下创建子节点 |
| `DELETE` (d) | 删除 | 删除子节点 |
| `ADMIN` (a) | 管理 | 设置 ACL、授权管理 |

权限可以组合，如：`READ | WRITE | DELETE`。

---

## 2.认证方式（Scheme）

ZooKeeper 支持以下几种身份认证方式：

### 1. `world`（开放权限）
- 格式：`world:anyone`
- 表示**任何人都能访问**，常用于测试或开放接口。
- 示例：`world:anyone` + `READ`

### 2. `auth`（已认证用户）
- 格式：`auth:`
- 表示所有通过 `addAuth` 认证的用户。
- 示例：`auth:` + `ALL`，允许所有认证过的用户访问。

### 3. `digest`（用户名+密码）
- 格式：`digest:username:base64_sha1(password)`
- 使用用户名+密码进行身份认证。
- 示例：
  ```shell
  
  addauth digest admin:123456
  ```
  设置 ACL：
  ```
  digest:admin:xxxxxxx + ALL
  ```

### 4. `ip`（按IP限制）
- 格式：`ip:ip_address`
- 限制某个 IP 地址访问。
- 示例：`ip:192.168.1.10` + `READ`

### 5. `x509`（基于证书）
- 使用证书方式进行身份识别（SSL/TLS，企业级较多）。

---

## 3.ACL 配置示例

### 示例 1：world:anyone 允许所有人读
```bash

setAcl /my_node world:anyone:r
```

### 示例 2：digest 用户 admin 拥有全部权限
```bash

addauth digest admin:123456
create /secure_node "data" digest:admin:base64_sha1_password:crwda
```

### 示例 3：IP 限制访问
```bash

setAcl /ip_node ip:192.168.1.100:cr
```

