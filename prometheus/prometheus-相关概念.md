# prometheus 相关概念

![](./images/architecture.svg)

## 1. **时间序列数据为中心（Time Series Oriented）**
Prometheus 的核心是**时间序列数据库（TSDB）**，每条监控数据都以：
- **metric 名称**
- **时间戳**
- **标签（Labels）**
- **样本值（Sample Value）**

的形式存储，比如：

```plaintext
http_requests_total{method="GET", handler="/api", status="200"} 1234 @timestamp
```

这意味着可以通过灵活的维度来细粒度分析任何指标。

---

## 2. **拉模型（Pull Model）为主**
Prometheus 主要使用 HTTP **定期拉取（pull）** target 端的指标（通常是 `/metrics`），而不是目标主动推送。

优点：
- 更加**中心化控制**，便于统一管理和调试
- 可结合服务发现自动发现目标（比如 Kubernetes、Consul）

当然也支持 Pushgateway 做少数推送场景。

---

## 3. **标签机制（Labels）**
Prometheus 用标签对指标进行多维度标识。

例如：

```plaintext
cpu_usage{instance="10.0.0.1:9100", job="node_exporter", core="0"} 0.37
```

标签是查询、聚合、分组的基础，是 Prometheus 灵活性的关键。

---

## 4. **PromQL 查询语言**
PromQL 是 Prometheus 的查询语言，非常强大，支持：
- 聚合（`sum`, `avg`, `count`, …）
- 条件过滤
- 函数（`rate`, `irate`, `increase`）
- 向量计算
- 子查询、时间范围

例：

```promql
rate(http_requests_total{job="api-server"}[5m])
```

这使得你可以用极其精细的方式分析和可视化数据。

---

## 5. **本地存储 + 远程扩展**
Prometheus 本地使用高效的 TSDB 存储：
- 数据按时间分块（chunks）
- 数据写入 WAL（Write Ahead Log）
- 以压缩的形式存储时间序列

同时支持：
- **远程写入（remote_write）**：发送数据到远程存储（如 Thanos、VictoriaMetrics）
- **远程读取（remote_read）**

---

## 6. **Alertmanager 告警集成**
Prometheus 内置告警机制，通过 **PromQL 表达式触发 Alert**，发送到 Alertmanager，后者支持：
- 去重、分组、抑制
- 多渠道通知（邮件、Slack、钉钉、Webhook 等）

---

## 7. **简洁部署 + 易扩展**
- 无需依赖外部系统（如 ZooKeeper）
- 单个二进制部署即可运行
- 通过 Exporter、Service Discovery、Relabeling 等方式灵活扩展

---





