# Redis基础-主从复制模式


## 1. 主从复制

Redis 主从复制（Replication）是一种将主节点的数据复制到一个或多个从节点的机制，从节点会持续与主节点保持同步，从而实现 **读写分离**、**数据冗余** 和 **高可用性** 的基础能力。

---

## 2. 典型架构图

```
                +--------+       +--------+       +--------+
                |        |       |        |       |        |
   Clients  →   | Master |  ←→   | Slave1 |  ←→   | Slave2 |
                |        |       |        |       |        |
                +--------+       +--------+       +--------+
                   ↑                               ↑
                   └──── Redis 主节点               └── Redis 从节点
```

---

## 3. 主从复制原理

### 启动流程：

1. **从节点发送命令**：
   ```bash
   SLAVEOF <master_ip> <master_port>
   ```

2. **建立 socket 连接**，主从互通。

3. **身份验证**（如果主设置了 `requirepass`）：
    - 从节点发送 `AUTH` 命令。

4. **全量同步（初次或失效）**：
    - 主节点执行 `RDB 快照` 并发送给从节点；
    - 主节点在快照期间的写命令记录在 `replication buffer`；
    - 从节点接收并加载 RDB 文件，然后 replay 缓冲区命令。

5. **部分重同步（PSYNC）**（网络抖动后重连）：
    - 从节点记录 `replication ID` 和 `offset`；
    - 重连时发出 `PSYNC <replicationid> <offset>`；
    - 主节点按需发送差异数据（增量复制）。

6. **保持同步**：
    - 主节点将每个写命令发送给从节点，完成实时同步。

---

## 4. 配置

### 主节点配置（redis.conf）：

```conf
# 默认即为主节点，不需要特别配置
port 6379
requirepass masterpass   # 可选：开启认证
```

### 从节点配置（redis.conf）：

```conf
replicaof 127.0.0.1 6379        # Redis 5.0+ 用 replicaof 替代 slaveof
masterauth masterpass           # 如主节点配置了密码需设置
requirepass slavepass           # 可选：从节点也设密码
```

> 提示：`slaveof` 在 Redis 5.0 前使用，5.0 起改名为 `replicaof`。

---

## 5. 相关命令

| 命令                              | 作用 |
|-----------------------------------|------|
| `SLAVEOF <ip> <port>`             | 从节点设置主节点地址 |
| `SLAVEOF NO ONE`                  | 取消复制，转为主节点 |
| `INFO replication`                | 查看当前复制状态 |
| `CONFIG SET replica-read-only no`| 允许从节点写入（不推荐） |
| `PSYNC` / `FULLRESYNC`            | 主从间同步协议指令（Redis 内部使用） |

---

## 6. 全量同步 vs 部分重同步

| 类型     | 触发条件                 | 数据量    | 是否阻塞 |
|----------|--------------------------|-----------|----------|
| 全量同步 | 初次复制、主重启、缓存丢失 | 整个数据  | 是       |
| 部分同步 | 断线重连 + offset 有效    | 差异数据  | 否       |

Redis 7 使用 `replication backlog buffer`（默认 1MB）保存主节点的写命令历史。

---

## 7. 主从状态查看示例

主节点执行：

```bash
127.0.0.1:6379> INFO replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6380,state=online,offset=121233,lag=0
slave1:ip=127.0.0.1,port=6381,state=online,offset=121233,lag=0
```

从节点执行：

```bash
127.0.0.1:6380> INFO replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:up
```

---


## 8. 常见问题

### 从节点能写入吗？
默认不能，从节点为只读模式。可以通过配置：
```conf
replica-read-only no
```
不建议打开，可能导致数据不一致。

### 支持一主多从吗？
支持。一个主节点可以有多个从节点。

### 可以从节点再挂一个从节点吗？
支持级联复制，从节点可以充当其他从节点的“主”。

### 主节点宕机后会怎样？
仅主从模式下不会自动切换。需配合 **哨兵** 或 **集群模式** 实现自动 failover。

---


