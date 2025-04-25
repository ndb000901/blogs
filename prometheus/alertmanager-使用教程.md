# alertmanager 使用教程


## 1. 基础配置

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--config.file` | `alertmanager.yml` | 指定 Alertmanager 的配置文件路径。 |
| `--storage.path` | `data/` | Alertmanager 用于持久化数据（如 silences、通知日志）的目录。 |
| `--data.retention` | `120h`（5天） | 数据保留时长，超时的 silences/通知日志会被清理。 |
| `--data.maintenance-interval` | `15m` | 进行数据 GC 和快照的时间间隔。 |

---

## 2. Silence（静默）相关参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--silences.max-silences` | `0`（不限制） | 最大 silences 数量，包含已过期的。如果设置为正数，则限制总数。 |
| `--silences.max-silence-size-bytes` | `0`（不限制） | 每个 silence 允许的最大字节数。 |

---

## 3. Alert 清理

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--alerts.gc-interval` | `30m` | Alertmanager 清理旧 alerts 的间隔时间。 |

---

## 4. Web 接口相关

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--web.listen-address` | `:9093` | Alertmanager Web 服务监听地址，可重复指定多个端口。 |
| `--web.config.file` | `""` | 使用 exporter-toolkit 配置 TLS、鉴权等功能。 |
| `--web.external-url` | 空 | 外部访问 Alertmanager 的 URL，影响链接生成。建议设置为完整 URL（含路径）。 |
| `--web.route-prefix` | 从 `--web.external-url` 推断 | Web API 路由前缀，影响 UI 和 API 地址。 |
| `--web.get-concurrency` | `0`（自动） | 限制并发 GET 请求数，防止 DoS 攻击。 |
| `--web.timeout` | `0`（无限） | HTTP 请求超时时间。 |

---

## 5. 内存控制

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--auto-gomemlimit.ratio` | `0.9` | GOMEMLIMIT 的比例（自动限制内存）。需 Go 1.19+。 |

---

## 6. 集群/HA 配置（用于高可用）

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--cluster.listen-address` | `0.0.0.0:9094` | Alertmanager 节点间监听地址（Gossip 协议）。空字符串表示禁用 HA 模式。 |
| `--cluster.advertise-address` | 空 | 集群内广播用的地址。 |
| `--cluster.peer` | 空 | 指定初始集群成员地址，可重复设置多个。 |
| `--cluster.peer-timeout` | `15s` | 发送通知前，等待其他节点响应的时间。 |
| `--cluster.gossip-interval` | `200ms` | Gossip 消息广播频率，越小传播越快但占带宽。 |
| `--cluster.pushpull-interval` | `1m` | Gossip 状态同步频率，间隔越小收敛越快。 |
| `--cluster.tcp-timeout` | `10s` | 节点连接、同步的 TCP 超时时间。 |
| `--cluster.probe-timeout` | `500ms` | 判断其他节点是否存活的 ACK 超时。 |
| `--cluster.probe-interval` | `1s` | 节点探测间隔，越小检测越快。 |
| `--cluster.settle-timeout` | `1m` | 集群建立后等待稳定的时间。 |
| `--cluster.reconnect-interval` | `10s` | 尝试重新连接失联节点的间隔。 |
| `--cluster.reconnect-timeout` | `6h` | 多久之后放弃重新连接某节点。 |
| `--cluster.tls-config` | 空 | 用于启用集群间 gossip 的 mutual TLS（实验性）。 |
| `--cluster.allow-insecure-public-advertise-address-discovery` | false | 允许广播公网 IP 地址（实验性）。 |
| `--cluster.label` | 空 | 为集群打标签（用于隔离不同集群）。 |

---

## 7. 实验特性

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--enable-feature` | 空 | 启用实验功能，例如 `receiver-name-in-metrics`、`classic-mode` 等。多个用逗号分隔。 |

---

## 8. 日志相关

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--log.level` | `info` | 日志等级：`debug`、`info`、`warn`、`error`。 |
| `--log.format` | `logfmt` | 日志格式：`logfmt` 或 `json`。 |

---

## 9. 其他

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--help`, `--help-long`, `--help-man` | 无 | 显示帮助信息。 |
| `--version` | 无 | 显示 Alertmanager 版本信息。 |

