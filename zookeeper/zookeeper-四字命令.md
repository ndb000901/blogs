# zookeeper 四字命令

## 1.配置

`zoo.cfg`

```properties
# 可配置需要的命令
4lw.commands.whitelist=*
4lw.commands.whitelist=stat,ruok,srvr,mntr,conf,cons,dump,wchs,wchp,wchc,envi,isro,srst,crst
```

## 2.命令

| 命令 | 作用 | 典型输出 / 功能 |
|-------|------|------------------|
| `ruok` | 健康检查 | 返回 `imok` 表示服务正常运行。 |
| `stat` | 查看服务器状态 | 输出连接数、模式（leader/follower/standalone）、延迟、请求数等。 |
| `srvr` | 更详细的服务器状态 | 类似 `stat`，但更结构化并显示服务器版本等信息。 |
| `mntr` | 监控信息（机器可读） | 提供 key=value 形式的数据，适合监控系统采集。 |
| `conf` | 显示服务器配置 | 展示当前运行时配置（clientPort、dataDir、server.x 配置等）。 |
| `envi` | 显示环境变量 | JVM 环境变量、主机名、操作系统信息等。 |
| `cons` | 查看连接信息 | 显示所有客户端连接和每个连接的状态（sessionId、IP、等待操作等）。 |
| `dump` | 查看会话和未处理请求 | 显示 session 数、ephemerals、待处理请求等。 |
| `wchc` | 显示哪些客户端在 watch 某节点 | 列出客户端 session 和它们 watch 的 znode。 |
| `wchp` | 显示哪些 znode 被 watch | 按 znode 显示有哪些客户端在 watch 它。 |
| `wchs` | Watch 汇总信息 | 显示 watcher 总数、watcher 所在 session 数量等。 |
| `isro` | 是否只读模式 | 返回 `ro` 表示只读模式，`rw` 表示读写模式（Leader、Follower 都是 `rw`）。 |
| `srst` | 重置统计信息 | 清除请求统计计数器（request count、latency等）。 |
| `crst` | 重置连接统计信息 | 清除客户端连接的统计信息。 |
| `dirs` | 显示数据目录大小 | 显示 dataDir、dataLogDir 的磁盘使用情况（某些版本支持）。 |
| `gtmk` | 获取当前 leader 的 zxid epoch（仅某些版本）| 显示当前集群 epoch 信息。 |

## 3.使用示例

`telnet`

```bash

telnet 192.168.43.242 12181

# 输入stat
Trying 192.168.43.242...
Connected to 192.168.43.242.
Escape character is '^]'.
stat
Zookeeper version: 3.9.3-c26634f34490bb0ea7a09cc51e05ede3b4e320ee, built on 2024-10-17 23:21 UTC
Clients:
 /192.168.43.242:42604[0](queued=0,recved=1,sent=0)

Latency min/avg/max: 0/0.0/0
Received: 3
Sent: 2
Connections: 1
Outstanding: 0
Zxid: 0x300000002
Mode: follower
Node count: 21
Connection closed by foreign host.
```

`nc`

```bash

echo stat | nc 192.168.43.242 12181

# 输出
Zookeeper version: 3.9.3-c26634f34490bb0ea7a09cc51e05ede3b4e320ee, built on 2024-10-17 23:21 UTC
Clients:
 /192.168.43.242:44546[0](queued=0,recved=1,sent=0)

Latency min/avg/max: 0/0.0/0
Received: 4
Sent: 3
Connections: 1
Outstanding: 0
Zxid: 0x300000002
Mode: follower
Node count: 21
```