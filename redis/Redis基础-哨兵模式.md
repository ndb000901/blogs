# Redis基础-哨兵模式


## 1. Redis 哨兵模式

**哨兵模式（Sentinel Mode）** 是 Redis 提供的一种**高可用方案**。哨兵进程（sentinel）会监控多个 Redis 实例（主节点和从节点），当发现主节点宕机时，会自动完成主从切换，并通知客户端更新主节点信息。

---

## 2. 架构图

```
        +-----------------+
        |     Client      |
        +-----------------+
                |
        +-----------------+
        |    Sentinel     |   ←←← 自动发现主从节点，通知客户端
        +-----------------+
           /     |      \
   ┌───────┘     |       └────────┐
   ▼             ▼                ▼
+--------+   +--------+      +--------+
| Master |   | Slave1 |      | Slave2 |
+--------+   +--------+      +--------+
      ▲           |                |
      └───────────┴────────────────┘
            主从复制（Replication）
```

---

## 3. 哨兵模式的核心功能

| 功能               | 说明 |
|--------------------|------|
| **监控**           | 持续 ping 主从节点，探测是否存活 |
| **通知**           | 向其他哨兵和客户端通知故障状态 |
| **自动故障转移**   | 主节点宕机后自动选举新主 |
| **服务发现**       | 客户端可通过 sentinel 获取当前主节点 IP |

---

## 4. 配置示例（sentinel.conf）

```conf
# sentinel 启动监听端口
port 26379

# 监控的主节点配置：格式为 sentinel monitor <name> <ip> <port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 2

# 设置认证密码（主从节点使用 requirepass 时）
sentinel auth-pass mymaster MyRedisPassword

# 主节点断线多少毫秒算“主观下线”
sentinel down-after-milliseconds mymaster 5000

# 发起 failover 前至少几个哨兵同意（必须 >= quorum）
sentinel failover-timeout mymaster 60000

# 一个哨兵同时可以向多少个从节点发出 slaveof 新主命令
sentinel parallel-syncs mymaster 1
```

---

## 5. 部署方式

假设有 1 主 2 从 3 哨兵，典型部署为：

```
节点        | 角色
------------|----------
Redis 1     | Master
Redis 2     | Slave
Redis 3     | Slave
Sentinel 1  | 哨兵进程
Sentinel 2  | 哨兵进程
Sentinel 3  | 哨兵进程
```

启动方式：

```bash
# 启动 Redis
redis-server redis.conf

# 启动哨兵
redis-sentinel sentinel.conf
```

---

## 6. 主观下线 vs 客观下线

| 类型     | 判定条件 | 发起者      | 用途         |
|----------|----------|-------------|--------------|
| SDown    | 自己 ping 不通 | 单个 sentinel | 主观判断     |
| ODown    | 多个 sentinel 都判定 SDown 且达 quorum 数 | 多个 sentinel 投票 | 触发故障转移 |

---

## 7. 故障转移流程

1. 主节点宕机
2. 某 sentinel 发现主观下线
3. quorum 个 sentinel 也发现 → 判定为客观下线
4. **选举 leader sentinel**
5. leader sentinel 执行：
    - 挑选一个 slave，发送 `SLAVEOF NO ONE` 变为新主
    - 其他 slave `SLAVEOF new-master`
    - 通知客户端新主地址
6. 转移完成

---

## 8. 客户端如何连接

客户端可使用以下方式：

- **手动更新**：应用监听 sentinel 通知，切换连接
- **中间件**：如 `Redis Sentinel Connection Pool`
- **高可用客户端**：
    - Java：Jedis、Lettuce 支持 sentinel
    - Python：redis-py-sentinel

示例（Jedis）：

```java
Set<String> sentinels = new HashSet<>();
sentinels.add("127.0.0.1:26379");

JedisSentinelPool pool = new JedisSentinelPool("mymaster", sentinels);
```

---

## 9. 优点

| 优势                   | 说明 |
|------------------------|------|
| 自动故障恢复           | 主节点挂了能自动转移 |
| 客户端服务发现         | 不需要重启服务 |
| 简单部署               | 无需复杂集群组件 |
| 与主从复制配合良好     | 架构清晰 |

---

## 10. 缺点

| 缺陷                     | 说明 |
|--------------------------|------|
| 不支持数据分片           | 单节点数据存储 |
| 一次只能管理一个主从组   | 多组需多个 sentinel 实例 |
| 自动化较粗糙             | 无智能故障预防机制 |
| 配置易错、排查困难       | 哨兵协作复杂 |

---

## 11. 建议

| 项目         | 建议                                               |
|--------------|----------------------------------------------------|
| 哨兵数量     | 至少部署 3 个，防止脑裂                             |
| 主从结构     | 至少 1 主 2 从（避免新主挂掉无后备）                |
| 设置密码     | 所有 Redis 节点设置 `requirepass`，哨兵配置同步     |
| 客户端支持   | 使用支持 sentinel 的客户端或中间件                  |
| 网络稳定性   | 哨兵节点部署在不同机器、机房，避免同时失联         |

---

## 12. 相关命令

```bash

# 哨兵基本操作
SENTINEL get-master-addr-by-name mymaster     # 获取主节点地址
SENTINEL masters                              # 列出所有主节点
SENTINEL slaves mymaster                      # 查看所有从节点
SENTINEL sentinels mymaster                   # 查看其他哨兵
```

---


