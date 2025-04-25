# prometheus 数据类型


## 1. **Counter（计数器）**
- **单调递增**（只能增加或重置为0）
- 适合计数场景，如请求次数、错误次数、任务完成数

**示例：**
```prometheus
http_requests_total{method="GET", handler="/api"} 1027
```

---

## 2. **Gauge（仪表盘）**
- **可增可减**，代表一个当前值
- 适合表示可变状态：内存使用量、当前连接数、温度等

**示例：**
```prometheus
memory_usage_bytes{instance="host1"} 623451136
```

---

## 3. **Histogram（直方图）**

- 用于统计值的分布，例如响应时间分布
- 会自动将值按区间（buckets）计数，还提供 `_sum` 和 `_count` 的额外数据

**示例（响应时间）：**
```prometheus
http_request_duration_seconds_bucket{le="0.1"} 24054
http_request_duration_seconds_bucket{le="0.2"} 33444
http_request_duration_seconds_count 144320
http_request_duration_seconds_sum 53423
```

---

## 4. **Summary（摘要）**
- 类似 Histogram，也用于观测分布，但客户端直接计算分位数（quantiles）
- 不适合在高负载服务中做全局聚合

**示例（响应时间摘要）：**
```prometheus
rpc_duration_seconds{quantile="0.5"} 0.05
rpc_duration_seconds{quantile="0.9"} 0.1
rpc_duration_seconds{quantile="0.99"} 0.5
rpc_duration_seconds_sum 1.7560473e+07
rpc_duration_seconds_count 2693
```

---
