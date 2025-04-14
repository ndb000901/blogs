# Redis基础-AOF持久化

## 1. AOF

**AOF（Append Only File）** 是 Redis 的另一种持久化机制，它会**以追加的方式记录每个写命令**，从而在服务器重启时重放命令恢复数据。

> 类似于数据库中的“**binlog**”，它可以实现更高的数据持久性。

---

## 2. AOF 的工作原理

1. 客户端发来写命令（如 `SET key value`）
2. Redis 立即将命令 **追加到 AOF 缓冲区**
3. 根据配置，将缓冲区 **写入磁盘**
4. Redis 重启时通过“**重放 AOF**”重建数据

AOF 记录的命令是 Redis 协议格式（RESP），可直接被 Redis 识别执行。

---

## 3. AOF 配置详解（`redis.conf`）

### 启用 AOF

```conf
appendonly yes
```

关闭 AOF：

```conf
appendonly no
```

### AOF 文件名

```conf
appendfilename "appendonly.aof"
```

默认就是这个文件名。

### 存储路径（与 RDB 共用）

```conf
dir /var/lib/redis
```

### AOF 重写配置

```conf
auto-aof-rewrite-percentage 100   # 当前文件比上次重写大 100%，则触发重写
auto-aof-rewrite-min-size 64mb    # 最小达到 64MB 才考虑重写
```

---

## 4. AOF 文件重写机制（Rewrite）

AOF 会不断变大，为避免文件膨胀，Redis 提供**重写机制**：

> 原理：将当前数据结构转换为最少命令的形式重新生成 AOF 文件。

例如：

```bash

SADD myset a
SADD myset b
SADD myset c
```

重写后变为：

```bash

SADD myset a b c
```

### 自动触发

满足如下两个条件：

1. 文件大于 `auto-aof-rewrite-min-size`
2. 文件比上次重写大 `auto-aof-rewrite-percentage`

### 手动触发

```bash

BGREWRITEAOF
```

由后台子进程进行，不阻塞主线程。

---

## 5. AOF 的刷盘策略（`appendfsync`）

刷盘策略决定 AOF 的可靠性和性能：

```conf

appendfsync always       # 每条写命令都刷盘（最安全，最慢）
appendfsync everysec     # 每秒刷盘（默认，性能+安全兼顾）
appendfsync no           # 不控制刷盘，交给 OS（最高性能，风险高）
```

**推荐：**`appendfsync everysec`（Redis 默认）

---

## 6. AOF 文件恢复流程

1. 启动 Redis
2. 检测到 `appendonly.aof` 文件存在
3. 依次读取并执行所有命令
4. 数据恢复完毕

> Redis 会自动检查 AOF 文件完整性，若尾部损坏，会自动尝试修复。

---

## 7. 混合持久化

```conf
aof-use-rdb-preamble yes
```

**混合持久化机制：**

1. AOF 文件开头是一段 RDB 快照（高效加载）
2. 后接写操作的 AOF 命令

**优点：** 启动恢复快，持久性高，性能好。

---

## 8. 优缺点

### 优点

- **更高的持久性**（相比 RDB）
- 命令追加形式，可读性强
- 支持重写优化，避免膨胀
- 可与 RDB 组合使用

### 缺点

- 文件体积大
- 恢复速度慢于 RDB（尤其命令多）
- 每秒写盘或频繁刷盘，性能开销大

---

## 9. RDB 与 AOF 对比

| 项目        | RDB                             | AOF                                  |
|-------------|----------------------------------|---------------------------------------|
| 数据持久性  | 低，可能丢失几分钟              | 高，最多丢 1 秒                       |
| 文件大小    | 小，二进制快照压缩              | 大，追加写命令                        |
| 启动恢复速度| 快                              | 慢                                   |
| 写入性能    | 高                              | 稍低（依赖 fsync 策略）              |
| 文件内容    | 二进制                          | 可读命令                             |
| 使用场景    | 性能敏感、冷备份                 | 数据安全要求高                       |

---

## 10. AOF 文件损坏

Redis 支持自动修复：

```bash

redis-check-aof --fix appendonly.aof
```

但修复可能丢失部分数据，建议定期备份。

---

