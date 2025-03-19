# zookeeper-ZNode详解

## 1.ZNode类别

| 类型 | 是否持久 | 是否自动删除 | 是否有序 |
|------|-----------|----------------|-------------|
| **持久节点（PERSISTENT）** | ✅ | ❌ | ❌ |
| **持久顺序节点（PERSISTENT_SEQUENTIAL）** | ✅ | ❌ | ✅ |
| **临时节点（EPHEMERAL）** | ❌ | ✅（会话断开自动删除） | ❌ |
| **临时顺序节点（EPHEMERAL_SEQUENTIAL）** | ❌ | ✅ | ✅ |

---

### 1.**持久节点（PERSISTENT）**
- 客户端创建后**一直存在**，除非显式删除。
- 会话断开，节点也不会被删除。


示例：
```bash

# create /demo/node1
Created /demo/node1
```

---

### 2.**持久顺序节点（PERSISTENT_SEQUENTIAL）**
- 在持久节点基础上，**自动在结尾追加递增编号**（由 ZooKeeper 自动生成）。
- 常用于 **排队、事务日志等场景**。

示例：

```bash

# create -s /demo/node1/r-
Created /demo/node1/r-0000000000

```

---

### 3.**临时节点（EPHEMERAL）**
- 节点与客户端会话绑定。
- 会话断开（崩溃或超时），节点自动被删除。
- 不允许有子节点（不能做父节点）。

示例：

```bash

# create -e /demo/node2
Created /demo/node2

# create -e -s /demo/node2/r-
Ephemerals cannot have children: /demo/node2/r-
```

---

### 4.**临时顺序节点（EPHEMERAL_SEQUENTIAL）**
- 结合“临时”+“有序”的特点。
- 会话断开自动删除，且自动加编号。

示例：

```bash

# create -e -s /demo/node3/r-
Created /demo/node3/r-0000000000

```

## 2.`Stat`结构详解

ZooKeeper 的 `Stat` 结构体用于存储 ZNode（ZooKeeper 数据节点）的元数据信息。


### **Stat 结构体字段详解**
`Stat` 结构体包含以下字段，每个字段都描述了 ZNode 的某些元数据：

| **字段**               | **数据类型** | **描述** |
|------------------------|------------|----------|
| `czxid`               | `long`     | ZNode **创建** 事务 ID（ZXID）。|
| `mzxid`               | `long`     | ZNode **最后一次更新** 的事务 ID（ZXID）。|
| `ctime`               | `long`     | ZNode **创建时间**（Unix 时间戳，毫秒）。|
| `mtime`               | `long`     | ZNode **最后修改时间**（Unix 时间戳，毫秒）。|
| `version`             | `int`      | ZNode **数据版本**（从 0 开始，每次数据变化 +1）。|
| `cversion`           | `int`      | ZNode **子节点版本**（子节点变化时增加）。|
| `aversion`           | `int`      | ZNode **ACL 版本**（ACL 变更时增加）。|
| `ephemeralOwner`      | `long`     | 如果是 **临时节点**，存储创建它的 **会话 ID**，否则为 `0`。|
| `dataLength`         | `int`      | ZNode **数据大小**（字节数）。|
| `numChildren`       | `int`      | ZNode **子节点数量**。|
| `pzxid`               | `long`     | ZNode **子节点的最后一个变更事务 ID（ZXID）**。|


#### 1. **事务 ID（ZXID）相关**
ZooKeeper 通过 **ZXID（Zookeeper Transaction ID）** 记录所有的状态变更。ZXID 是 **全局递增** 的，每个事务都有唯一的 ZXID。

- `czxid`（**创建 ZXID**）  
  该 ZNode **创建** 时的事务 ID。
- `mzxid`（**修改 ZXID**）  
  该 ZNode **最后一次修改** 时的事务 ID。
- `pzxid`（**子节点 ZXID**）  
  该 ZNode **子节点** 发生变化（新增、删除子节点）时的事务 ID。

**示例**：
如果一个 ZNode 的 `czxid=0x100000002`，`mzxid=0x100000005`，说明：
- 该 ZNode 创建时的事务 ID 为 `0x100000002`。
- 该 ZNode **至少被修改过一次**（最后一次修改的事务 ID 为 `0x100000005`）。
- 如果 `pzxid=0x100000007`，则表明 **子节点最近发生过变更**（比如新增或删除子节点）。

---

#### 2. **时间戳（ctime & mtime）**
- `ctime`（**创建时间**）  
  该 ZNode **被创建时的时间戳**，单位是 **毫秒**（自 1970 年 1 月 1 日起）。
- `mtime`（**修改时间**）  
  该 ZNode **最后一次被修改** 时的时间戳，单位是 **毫秒**。


#### 3. **版本号（version、cversion、aversion）**
- `version`（**数据版本**）：ZNode 的 **数据变更次数**，每次 `setData` 更新 ZNode 数据时递增。
- `cversion`（**子节点版本**）：ZNode **子节点** 发生变化（新增/删除子节点）时递增。
- `aversion`（**ACL 版本**）：ZNode **ACL 变更** 时递增。

**示例**：
如果 `version=3`，`cversion=5`，`aversion=0`，表示：
- 该 ZNode **数据修改过 3 次**。
- 该 ZNode **子节点发生过 5 次变化**（可能新增、删除子节点）。
- 该 ZNode **ACL 没有被修改过**（`aversion=0`）。

---

#### 4. **临时节点信息**
- `ephemeralOwner`（**临时节点会话 ID**）：
  - **如果是临时节点**，存储创建该节点的会话 ID。
  - **如果是持久节点**，则该值为 `0`。

**示例**：
- 如果 `ephemeralOwner=0x12345678`，表示该节点是一个临时节点，并且是会话 ID 为 `0x12345678` 的客户端创建的。
- 如果 `ephemeralOwner=0`，说明该节点是 **持久节点**。

---

#### 5. **数据和子节点**
- `dataLength`（**数据大小**）：该 ZNode **存储的数据大小**，单位是字节。
- `numChildren`（**子节点数量**）：该 ZNode 直接子节点的个数。

**示例**：
- `dataLength=256` 表示该节点存储了 **256 字节** 的数据。
- `numChildren=3` 表示该节点 **有 3 个直接子节点**。

---

#### 代码示例

```java
import org.apache.zookeeper.*;

public class ZooKeeperStatExample {
    public static void main(String[] args) throws Exception {
        ZooKeeper zk = new ZooKeeper("localhost:2181", 3000, null);

        // 获取 ZNode 数据，同时获取 Stat 结构信息
        Stat stat = new Stat();
        byte[] data = zk.getData("/my-node", false, stat);

        // data 可能为null
        if (data != null) {
            System.out.println("ZNode 数据：" + new String(data));
        }
        System.out.println("创建 ZXID: " + stat.getCzxid());
        System.out.println("最后修改 ZXID: " + stat.getMzxid());
        System.out.println("创建时间: " + stat.getCtime());
        System.out.println("最后修改时间: " + stat.getMtime());
        System.out.println("数据版本: " + stat.getVersion());
        System.out.println("子节点版本: " + stat.getCversion());
        System.out.println("ACL 版本: " + stat.getAversion());
        System.out.println("临时节点会话 ID: " + stat.getEphemeralOwner());
        System.out.println("数据长度: " + stat.getDataLength());
        System.out.println("子节点数量: " + stat.getNumChildren());

        zk.close();
    }
}
```
