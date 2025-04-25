# Alertmanager 安装教程

## 1. 下载

```bash

wget https://github.com/prometheus/alertmanager/releases/download/v0.28.1/alertmanager-0.28.1.linux-amd64.tar.gz
```

## 2. 解压

```bash

tar -zxvf alertmanager-0.28.1.linux-amd64.tar.gz
```


## 3. 环境变量

```bash

# vim ~/.zshrc
export PATH=$PATH:/home/hello/local/alertmanager-0.28.1

# 刷新
source ~/.zshrc
```

## 4. 配置

[配置详解](./alertmanager-配置详解.md)

```yml
route:
  group_by: ['alertname']         # 将具有相同 alertname 的告警归为一组，方便合并通知（减少骚扰）

  group_wait: 30s                 # 初次收到一组告警时，等待 30 秒收集更多告警后一起发送通知

  group_interval: 5m             # 同一组中已有告警后，新增告警需要等待 5 分钟后再发送（防止频繁推送）

  repeat_interval: 1h            # 即使告警未解决，也要每隔 1 小时重新发送一次（防止遗漏）

  receiver: 'web.hook'           # 所有未被子路由拦截的告警，默认发给 'web.hook' 接收器
receivers:
  - name: 'web.hook'              # 接收器名称，对应 route 中的 receiver 字段
    webhook_configs:              # 使用 Webhook 方式发送通知
      - url: 'http://192.168.43.242:18080/api/alert'  # 通知将以 POST 请求发送到此 URL
inhibit_rules:
  - source_match:
      severity: 'critical'        # 来源告警匹配条件：严重等级为 critical 的告警
    target_match:
      severity: 'warning'         # 目标告警匹配条件：严重等级为 warning 的告警
    equal: ['alertname', 'dev', 'instance']  # 如果 source 和 target 的这三个标签值一致，则 warning 告警会被抑制

```

## 5. 启动

```bash

./alertmanager --config.file=alertmanager.yml --web.listen-address=192.168.43.242:9093
```

### web页面

```bash

http://192.168.43.242:9093/
```

## 6. prometheus 配置

`alert_rules.yml`

```yml
groups:
  - name: pushgateway-alerts
    rules:
      - alert: SomeMetricTooHigh
        expr: some_metric > 1000
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "some_metric is too high"
          description: "The value of some_metric is above 1000 (current value: {{ $value }})"
```

`prometheus.yml`

```yml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - 192.168.43.242:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - alert_rules.yml
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["192.168.43.242:9090"]
       # The label name is added as a label `label_name=<label_value>` to any timeseries scraped from this config.
        labels:
          app: "prometheus"

  - job_name: "pushgateway"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["192.168.43.242:9091"]
       # The label name is added as a label `label_name=<label_value>` to any timeseries scraped from this config.
        labels:
          app: "pushgateway"
```


## 7. webhook 示例

`AlertController.java`

```java
@RestController
public class AlertController {

    @RequestMapping("/api/alert")
    public Map<String, String> alert(@RequestBody Map<Object, Object> body) {
        System.out.println(body);
        Map<String, String> result = new HashMap<>();
        result.put("code", "200");
        result.put("message", "success");
        return result;
    }
}
```

`application.yml`

```yml

server:
  port: 18080
spring:
  application:
    name: alert
```

### 输出

```json

{
  "receiver": "web.hook",
  "status": "firing",
  "alerts": [
    {
      "status": "firing",
      "labels": {
        "alertname": "SomeMetricTooHigh",
        "app": "pushgateway",
        "exported_instance": "instance-1",
        "exported_job": "test-pushgateway",
        "instance": "192.168.43.242:9091",
        "job": "pushgateway",
        "label1": "value1",
        "severity": "warning"
      },
      "annotations": {
        "description": "The value of some_metric is above 1000 (current value: 4002)",
        "summary": "some_metric is too high"
      },
      "startsAt": "2025-04-25T18:12:46.456Z",
      "endsAt": "0001-01-01T00:00:00Z",
      "generatorURL": "http://dev:9090/graph?g0.expr=some_metric+%3E+1000&g0.tab=1",
      "fingerprint": "db09b1037b4282b0"
    }
  ],
  "groupLabels": {
    "alertname": "SomeMetricTooHigh"
  },
  "commonLabels": {
    "alertname": "SomeMetricTooHigh",
    "app": "pushgateway",
    "exported_instance": "instance-1",
    "exported_job": "test-pushgateway",
    "instance": "192.168.43.242:9091",
    "job": "pushgateway",
    "label1": "value1",
    "severity": "warning"
  },
  "commonAnnotations": {
    "description": "The value of some_metric is above 1000 (current value: 4002)",
    "summary": "some_metric is too high"
  },
  "externalURL": "http://dev:9093",
  "version": "4",
  "groupKey": "{}:{alertname=\"SomeMetricTooHigh\"}",
  "truncatedAlerts": 0
}

```