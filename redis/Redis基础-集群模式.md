# Redis基础-集群模式

## 1. Redis 集群模式

**Redis Cluster** 是 Redis 提供的一种分布式部署方案，用于在多个节点之间实现数据自动分片（Sharding）和高可用（Failover）。它允许用户在多个 Redis 实例中存储键值对，并且能自动处理主节点故障转移。

---

## 2. Redis 集群架构

Redis 集群由多个 Redis 节点组成，节点分为：

- **主节点（Master）**：负责读写请求、管理哈希槽。
- **从节点（Replica）**：备份主节点数据，实现高可用。

一个典型的 Redis Cluster 最小配置为：
- **3 个主节点 + 3 个从节点**（推荐至少 6 个节点）

### 架构图：

```
           +-------------+        +-------------+        +-------------+
           |  Master A   |        |  Master B   |        |  Master C   |
           |  Slot 0~5460|        |5461~10922   |        |10923~16383  |
           +-------------+        +-------------+        +-------------+
              |   ▲                    |   ▲                   |   ▲
              ▼   |                    ▼   |                   ▼   |
         Replica A1                Replica B1             Replica C1
```

---

## 3. 数据分片原理（哈希槽机制）

Redis 集群将 **所有的 key** 根据 CRC16 校验值分成 **16384 个槽位（slots）**：

```bash

slot = CRC16(key) % 16384
```

- 每个主节点负责一部分槽；
- 键通过槽映射到节点；
- 集群保证每个 slot 有唯一主节点负责。

---

## 4. 部署方式

### 推荐节点数：`3 主 + 3 从`

### 启动命令（每个节点端口不同）：

```bash

redis-server ./redis.conf --port 7000 --cluster-enabled yes --cluster-config-file nodes-7000.conf --cluster-node-timeout 5000 --appendonly yes
```

### 创建集群（以 redis-cli 为例）：

```bash

# 使用 redis-cli 创建集群（需先启动各个 redis-server）
redis-cli --cluster create \
  127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 \
  127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
  --cluster-replicas 1
```

参数说明：
- `--cluster-replicas 1`：为每个主节点分配 1 个从节点。

---

## 5. 配置文件（集群关键项）

```conf
# redis.conf 中关键配置项
port 7000
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
protected-mode no   # 集群部署时推荐关闭
```

---

## 6. 常用命令

| 命令                                  | 说明 |
|---------------------------------------|------|
| `CLUSTER NODES`                       | 查看当前集群节点列表 |
| `CLUSTER INFO`                        | 查看集群整体状态 |
| `CLUSTER SLOTS`                       | 查看槽分配 |
| `CLUSTER KEYSLOT <key>`              | 查看 key 的 slot |
| `CLUSTER GETKEYSINSLOT <slot> <count>`| 查询指定槽中的 key |
| `CLUSTER FAILOVER`                   | 强制从节点升主 |
| `CLUSTER MEET <ip> <port>`           | 添加新节点 |
| `CLUSTER FORGET <node_id>`           | 移除节点 |
| `CLUSTER REPLICATE <node_id>`        | 设置当前节点为指定主节点的副本 |

---

## 7. 故障转移（Failover）

- 主节点故障时，其对应的从节点会通过投票 **自动提升为主节点**；
- Redis 使用 **Gossip 协议** 定期通信并检测节点状态；
- 故障判定过程：

| 步骤 | 描述 |
|------|------|
| 1    | 从节点监测主节点故障（PING 超时） |
| 2    | 多个节点达成一致（超过半数） |
| 3    | 从节点触发选主流程，提升自己为主 |
| 4    | 通知集群其他节点更新结构 |

> 提示：需要至少 `n/2+1` 个主节点存活，集群才不会不可用。

---

## 8. 集群限制与注意事项

| 限制项                        | 描述 |
|-------------------------------|------|
| 不支持多 key 跨 slot 操作     | 比如 `MGET key1 key2`，除非 key1 和 key2 在同一 slot |
| 不支持 Lua 脚本跨 slot        | 脚本中的多个 key 必须属于同一 slot |
| 不支持事务跨 slot             | MULTI/EXEC 操作仅支持同 slot |
| 网络 split brain 风险         | 建议使用哨兵或其他高可用机制做监控 |
| 最少节点数                    | 至少 3 主节点，才能做主容灾投票 |

---

## 9. 槽迁移（reshard）

Redis Cluster 支持在线迁移槽位与 key，用于节点扩容或重分配。

```bash

redis-cli --cluster reshard 127.0.0.1:7000
```

- 输入迁移 slot 数量；
- 指定目标节点；
- 确定迁移来源。

---

### 9.1 整体思路

1. 将新节点加入集群。
2. 从已有节点迁移部分 **哈希槽（slot）** 到新节点。
3. **迁移槽内的数据**。
4. 更新集群节点配置，让所有节点知道槽的新归属。

---

### 9.2 操作步骤（以 `redis-cli` 为例）

假设原集群中已有节点 A 和 B，现在要加入新节点 C：

---

#### 9.2.1 启动新节点 C（端口 7003）

```bash

redis-server ./redis.conf --port 7003
```

配置中确保开启了集群：

```ini
port 7003
cluster-enabled yes
cluster-config-file nodes-7003.conf
cluster-node-timeout 5000
appendonly yes
```

---

#### 9.2.2 将新节点加入集群

假设现有节点是 7000：

```bash

redis-cli --cluster add-node 127.0.0.1:7003 127.0.0.1:7000
```

> 上述命令让 7003 成为一个 **新的主节点**，加入已有的集群中。

---

#### 9.2.3 选择迁移的哈希槽

比如，我们准备把节点 A（7000）中的 100 个槽迁移到新节点 C（7003）：

```bash

redis-cli --cluster reshard 127.0.0.1:7000
```

然后根据提示：

- 输入要迁移的 slot 数：`100`
- 输入接收槽的目标节点 ID（7003 的 ID）
- 输入源节点 ID（可以留空代表从多个节点迁移）
- 是否自动确认：`yes`

Redis 会自动把对应槽和数据迁移过去。

---

### 9.3 迁移槽的底层操作原理

Redis Cluster 槽迁移分 2 步：

1. **迁移槽的归属**：
    - 修改槽分布信息（`CLUSTER SETSLOT`）；
2. **迁移槽中的 key 数据**：
    - 使用 `MIGRATE` 命令逐个 key 拷贝到目标节点；
    - 携带 TTL、类型、值等完整信息；
    - 并从源节点删除。

> Redis 会自动处理与客户端连接时的 `ASK` 重定向。

---


### 9.4 验证迁移结果

查看当前槽分布：

```bash

redis-cli -p 7000 cluster slots
```







