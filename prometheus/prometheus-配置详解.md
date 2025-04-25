# prometheus 配置详解

[官方文档](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

## 1. `global:` 全局配置

这些设置是所有 job 的默认值，但每个 `scrape_config` 都可以单独覆盖它们。

```yaml
global:
  # 默认抓取周期，表示 Prometheus 多久抓取一次指标数据。可被各 job 覆盖。
  scrape_interval: 1m  # 默认1分钟抓取一次

  # 抓取超时时间，必须小于或等于 scrape_interval。
  scrape_timeout: 10s  # 默认超时时间为10秒

  # 抓取过程中协商使用的协议列表，通常不需要修改。
  scrape_protocols: [OpenMetricsText1.0.0, OpenMetricsText0.0.1, PrometheusText0.0.4]

  # 规则评估周期（主要用于规则文件中的 recording 或 alert 规则）。
  evaluation_interval: 1m

  # 向后偏移评估时间戳，主要用于避免“数据尚未到达”的问题，特别是 remote write 场景。
  rule_query_offset: 0s

  # 向外部系统（例如 federation、Alertmanager）发送的所有时间序列或告警附加的标签。
  external_labels:
    cluster: "prod-cluster"
    env: "prod"

  # PromQL 查询日志保存位置。
  query_log_file: "/var/log/prometheus/query.log"

  # 抓取失败日志文件。
  scrape_failure_log_file: "/var/log/prometheus/scrape_failures.log"

  # 抓取响应体大小上限（单位如 100MB），超出将视为失败，0 表示无限制。
  body_size_limit: 0

  # 每次抓取中允许的最大样本数，超过会导致抓取失败，0 表示无限制。
  sample_limit: 0

  # 每个样本最多的 label 数量，超过会失败。
  label_limit: 0

  # 每个 label 的名字最大字节长度。
  label_name_length_limit: 0

  # 每个 label 的值最大字节长度。
  label_value_length_limit: 0

  # 每个 scrape_config 允许的最大目标数（target）。
  target_limit: 0

  # 被 relabel 丢弃的目标，Prometheus 会保留多少个在内存中，用于 UI 显示。
  keep_dropped_targets: 0

  # 指定 metric 和 label 名称的校验模式："utf8" 或 "legacy"（仅允许数字、字母、下划线、冒号）
  metric_name_validation_scheme: "utf8"
```

---

### `runtime:` Go 运行时相关参数

```yaml
runtime:
  # GOGC 控制 Go GC 频率，值越小 GC 越频繁，CPU 占用越高。
  gogc: 75
```

---

### `rule_files:` 规则文件列表

```yaml
rule_files:
  - "rules/*.yml"
```

支持 glob 通配符。Prometheus 会加载匹配的所有 rule 文件。

---

### `scrape_config_files:` 抓取配置文件列表

```yaml
scrape_config_files:
  - "scrape_configs/*.yml"
```

支持多个 job 单独配置拆分文件，用于模块化管理。

---

### `scrape_configs:` 抓取目标配置列表

```yaml
scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
```

用于定义实际的抓取任务和目标，格式很多，支持 service discovery、relabel 等。

---

### `alerting:` 告警相关配置

```yaml
alerting:
  # 对 alert label 进行 relabel 操作。
  alert_relabel_configs:
    - source_labels: [__name__]
      regex: ".*"
      action: keep

  # Alertmanager 地址配置
  alertmanagers:
    - static_configs:
        - targets: ["localhost:9093"]
```

---

### `remote_write:` 远程写入配置（写入外部存储）

```yaml
remote_write:
  - url: "http://remote-storage:9201/write"
```

---

### `otlp:` OpenTelemetry 接收配置

```yaml
otlp:
  # Prometheus 会将资源属性添加到 target_info 指标中
  promote_resource_attributes: ["service.name"]

  # OpenTelemetry 指标转 Prometheus 格式的策略
  translation_strategy: "UnderscoreEscapingWithSuffixes"

  # 是否保留 service.name 等资源属性
  keep_identifying_resource_attributes: true
```

---

### `remote_read:` 远程读取配置（从远程系统读取历史数据）

```yaml
remote_read:
  - url: "http://remote-storage:9201/read"
```

---

### `storage:` 存储相关配置

```yaml
storage:
  tsdb:
    # 可以配置例如 retention_time、wal settings 等
  exemplars:
    # 配置 exemplar 数据存储
```

---

### `tracing:` Tracing 配置

```yaml
tracing:
  # 如使用 Jaeger、OTLP trace exporter 等时在此配置
```

## 2. `scrape_config`

>scrape_configs 决定了 Prometheus 去哪里抓取 metrics、多久抓一次、用什么协议抓、抓取的路径、附加什么标签、如何处理服务发现等等。


```yaml
scrape_configs:
  - job_name: 'my_app'

    # 每15秒抓取一次（默认是 global 的设置）
    scrape_interval: 15s

    # 如果抓取超过10秒没响应就超时（不能大于 scrape_interval）
    scrape_timeout: 10s

    # 使用 http 协议（默认）
    scheme: http

    # 关闭压缩（默认是 true）
    enable_compression: false

    # 如果采集返回空或类型不对，可以尝试切换协议
    fallback_scrape_protocol: PrometheusText0.0.4

    # honor_labels 设为 true 表示保留目标返回的标签
    honor_labels: true

    # honor_timestamps 为 false 则忽略目标中的时间戳（默认是 true）
    honor_timestamps: false

    # 控制 TSDB 中是否追踪带时间戳的 series 的 staleness
    track_timestamps_staleness: true

    # 指定抓 metrics 的路径（默认 /metrics）
    metrics_path: /custom_metrics

    # 可以配置 URL 参数
    params:
      token: ['123456']

    # 控制样本、标签数量等限制，防止 OOM 或攻击
    sample_limit: 5000
    label_limit: 64
    label_name_length_limit: 128
    label_value_length_limit: 1024

    # 配置目标（这里是静态的 IP 地址）
    static_configs:
      - targets: ['localhost:8080', 'localhost:9090']
        labels:
          env: dev
          app: my_service

    # relabel_configs 可用于修改 targets，例如过滤掉不需要的目标
    relabel_configs:
      - source_labels: [__address__]
        regex: '.*:8080'
        action: keep

    # metric_relabel_configs 可对抓回来的 metrics 做处理，例如过滤掉某些 metric
    metric_relabel_configs:
      - source_labels: [__name__]
        regex: 'go_gc_duration_seconds.*'
        action: drop
```

