# Redis配置文件详解


## 1. 通用配置

```ini
# 是否以守护进程方式运行，默认 no。设置为 yes 时，Redis 将在后台运行。
daemonize yes

# PID 文件路径，记录 Redis 进程的 ID。
pidfile /var/run/redis_6379.pid

# 日志级别，可选项：debug、verbose、notice、warning。默认 notice。
loglevel notice

# 日志文件路径，空字符串表示输出到标准输出。
logfile ""

# 数据库数量，默认 16 个，编号从 0 到 15。
databases 16

# 是否在启动时显示 Redis 的 ASCII 艺术 Logo，默认 yes。
always-show-logo yes
```

---

## 2. 网络配置

```ini
# 绑定的 IP 地址，默认仅监听本地回环地址。可绑定多个地址，用空格分隔。
bind 127.0.0.1

# 监听的端口号，默认 6379。设置为 0 表示不监听 TCP 连接。
port 6379

# TCP 半连接队列长度，影响高并发连接的处理能力。
tcp-backlog 511

# 客户端空闲超时时间（秒），0 表示不超时。
timeout 0

# TCP 保活时间（秒），用于检测死连接。
tcp-keepalive 300

# 是否启用保护模式，默认 yes。启用时，未设置 bind 或密码时仅允许本地连接。
protected-mode yes
```

---

## 3. 持久化配置

### 3.1 RDB 快照

```ini
# 设置保存快照的条件：在指定时间内有指定次数的写操作。
save 900 1
save 300 10
save 60 10000

# RDB 文件名，默认 dump.rdb。
dbfilename dump.rdb

# RDB 文件保存路径。
dir ./

# 当 RDB 保存失败时是否停止写操作，默认 yes。
stop-writes-on-bgsave-error yes
```

### 3.2 AOF 持久化

```ini
# 是否启用 AOF 持久化，默认 no。
appendonly no

# AOF 文件名，默认 appendonly.aof。
appendfilename "appendonly.aof"

# AOF 同步策略：always、everysec、no。
appendfsync everysec

# 当 AOF 重写失败时是否继续写入，默认 yes。
no-appendfsync-on-rewrite no

# AOF 文件重写触发条件：增长率百分比。
auto-aof-rewrite-percentage 100

# AOF 文件重写触发条件：最小增长大小。
auto-aof-rewrite-min-size 64mb

# 是否在 AOF 文件加载时忽略尾部不完整的数据，默认 yes。
aof-load-truncated yes

# 是否启用混合持久化（RDB + AOF），默认 yes。
aof-use-rdb-preamble yes
```

---

## 4. 安全配置

```ini
# 设置客户端连接密码。
requirepass yourpassword

# 设置主从复制的认证密码。
masterauth yourpassword
```

---

## 5. 复制配置

```ini
# 设置主服务器的地址和端口。
# replicaof <masterip> <masterport>

# 主从复制时的认证密码。
# masterauth yourpassword

# 从服务器是否只读，默认 yes。
replica-read-only yes

# 从服务器连接主服务器的超时时间（秒）。
repl-timeout 60

# 主服务器发送 PING 的间隔时间（秒）。
repl-ping-replica-period 10
```

---

## 6. 内存管理

```ini
# 最大内存使用限制，0 表示不限制。
maxmemory 0

# 内存淘汰策略：
# noeviction、allkeys-lru、volatile-lru、allkeys-random、volatile-random、volatile-ttl
maxmemory-policy noeviction

# LRU 算法采样数，影响淘汰策略的精度。
maxmemory-samples 5
```

---

## 7. 慢查询日志

```ini
# 慢查询阈值（微秒），超过该值的命令将被记录。
slowlog-log-slower-than 10000

# 慢查询日志的最大长度，超过该长度后最旧的记录将被删除。
slowlog-max-len 128
```

---

## 8. 延迟监控

```ini
# 延迟监控的阈值（毫秒），0 表示关闭延迟监控。
latency-monitor-threshold 0
```

---

## 9. 模块加载

```ini
# 加载模块的路径和名称。
# loadmodule /path/to/module.so
```

---

## 10. 集群配置

```ini
# 是否启用集群模式，默认 no。
cluster-enabled no

# 集群配置文件名，Redis 会自动生成和维护。
cluster-config-file nodes-6379.conf

# 集群节点超时时间（毫秒），用于判断节点是否失联。
cluster-node-timeout 15000

# 是否要求集群所有槽位都被覆盖，默认 yes。
cluster-require-full-coverage yes

# 集群节点迁移的最小副本数，默认 1。
cluster-migration-barrier 1

# 集群节点在主节点宕机时是否进行故障转移，默认 no。
cluster-replica-no-failover no

# 集群节点在集群不可用时是否仍然提供读服务，默认 no。
cluster-allow-reads-when-down no
```

---

## 11 配置

[配置文件-v7.2.7](https://github.com/redis/redis/blob/7.2.7/redis.conf)