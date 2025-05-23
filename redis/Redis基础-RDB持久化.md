# Redis基础-RDB持久化

## 1. RDB

**RDB（Redis Database）** 是 Redis 提供的一种**快照式持久化机制**，将某一时刻的 Redis 数据写入 `.rdb` 二进制文件，便于后续恢复数据。

> 简单理解：RDB 是 Redis 的“全量备份”，类似“数据库快照”。

---

## 2. RDB 文件结构

`.rdb` 是一种**紧凑的二进制格式**，结构如下：

```
REDIS0009         # 文件头（版本标识）
SELECTDB          # 每个数据库开始前的标识
Key-Value Pair    # 实际数据，编码压缩
EOF               # 文件结束标志
CRC64             # 数据完整性校验
```

Redis 会在内存数据满足某个条件时触发快照写入。

---

## 3. RDB 的触发方式

### 3.1 手动触发

- `SAVE`：阻塞式快照，保存到 `dump.rdb`
- `BGSAVE`：**后台子进程**执行，不阻塞主线程

```bash

SAVE
BGSAVE
```

### 3.2 自动触发（配置文件）

在 `redis.conf` 中：

```conf
save 900 1       # 900 秒内至少 1 个 key 被修改
save 300 10      # 300 秒内至少 10 个 key 被修改
save 60 10000    # 60 秒内至少 10000 个 key 被修改
```

可以写多行；也可以禁用：

```conf
save ""
```

---

## 4. 相关配置详解

| 配置项                        | 含义 |
|-------------------------------|------|
| `save`                        | 自动触发 BGSAVE 的条件 |
| `stop-writes-on-bgsave-error` | RDB 保存失败时是否拒绝写入（默认 yes）|
| `rdbcompression`              | 是否使用 LZF 压缩数据（默认 yes）|
| `rdbchecksum`                 | 写入时是否附加 CRC64 校验（默认 yes）|
| `dbfilename`                  | RDB 文件名，默认是 `dump.rdb` |
| `dir`                         | RDB 文件保存路径 |

---

## 5. RDB 文件生成流程（BGSAVE）

1. 主线程 `fork()` 出子进程
2. 子进程开始将数据写入临时 RDB 文件
3. 写完后原子性重命名为 `dump.rdb`
4. 子进程退出，主进程继续工作

> 注：fork 过程中会导致额外的内存使用（写时复制）

---

## 6. RDB 恢复数据

当 Redis 启动时，如果检测到 `dump.rdb` 文件，会自动加载数据进内存。

恢复过程无需人工干预：

```bash

redis-server --dir /your/path --dbfilename dump.rdb
```

---

## 7. RDB 的优点

- **性能好**：持久化过程在子进程中进行，不影响主线程
- **适合冷备份**：可周期性保存、拷贝到异地
- **恢复快**：加载时是全量内存数据，一次性恢复

---

## 8. RDB 的缺点

- **非实时**：一旦宕机，会丢失上一次快照之后的数据（数据不一致）
- **fork 有代价**：大数据量下频繁 `fork` 子进程会导致内存压力增大

---

## 9. 建议

- 建议 **RDB + AOF 混合持久化**：提升持久性，兼顾恢复速度
- 小数据量系统可以仅开启 RDB，提高性能
- 大数据量建议关闭 `save` 条件，改为用 `cron` 定时触发 `BGSAVE`
- 注意服务器内存是否支持 `fork`，避免 OOM（Out Of Memory）

---


