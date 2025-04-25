# alertmanager 配置详解

[官方文档](https://prometheus.io/docs/alerting/latest/configuration/)


## 1. `global` 全局配置（主要用于通知渠道的默认参数）

```yaml
global:
  # 邮件发送人（From 字段），会出现在邮件头部。
  smtp_from: "alertmanager@example.com"

  # SMTP 服务器地址及端口，例如 smtp.163.com:465（TLS），smtp.qq.com:25（非加密）
  smtp_smarthost: "smtp.example.com:587"

  # SMTP 会话的 Hello 名称，默认是 localhost。
  smtp_hello: "alertmanager"

  # SMTP 登录用户名（如果需要身份认证）
  smtp_auth_username: "your-email@example.com"

  # SMTP 登录密码（可以直接写也可以用下面的 *_file 从文件读取）
  smtp_auth_password: "password"

  # 用于登录身份认证（用于 PLAIN 模式）
  smtp_auth_identity: ""

  # SMTP 使用的加密模式：默认必须使用 TLS。
  smtp_require_tls: true

  # Slack Webhook 地址（全局默认，如果 receiver 没指定）
  slack_api_url: 'https://hooks.slack.com/services/XXX/YYY/ZZZ'

  # 企业微信 Webhook 相关（可用于消息推送）
  wechat_api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'
  wechat_api_secret: 'xxx'
  wechat_api_corp_id: 'xxx'

  # Alertmanager 默认的告警过期时间（仅在告警没有 EndsAt 字段时生效）
  resolve_timeout: 5m
```

---

## 2. `templates` 自定义模板路径

```yaml
templates:
  - "templates/*.tmpl"  # 通常是 Go 模板语法，用于自定义邮件、飞书、钉钉消息格式等
```

---

## 3. `route` 告警路由策略

```yaml
route:
  receiver: 'default-receiver'    # 默认的接收器（如未匹配任何子路由）

  group_by: ['alertname']         # 按哪些标签分组，多个告警合并通知
  group_wait: 30s                 # 初次分组等待时间
  group_interval: 5m             # 同一组重复告警之间的间隔时间
  repeat_interval: 4h            # 告警重复发送的周期

  # 子路由（可以根据 alertname、severity 等进行路由分发）
  routes:
    - match:
        severity: critical
      receiver: 'email-alert'
      continue: true
```

---

## 4. `receivers` 通知接收器（可以是邮箱、Slack、Webhook 等）

```yaml
receivers:
  - name: 'email-alert'
    email_configs:
      - to: 'devops@example.com'
        send_resolved: true        # 是否发送恢复通知

  - name: 'webhook-alert'
    webhook_configs:
      - url: 'http://localhost:8080/alert'
        send_resolved: true

  - name: 'wecom'
    wechat_configs:
      - corp_id: 'wx123'
        to_user: 'user1|user2'
        agent_id: '1000002'
        api_secret: 'xxxxx'
        send_resolved: true
```

---

## 5. `inhibit_rules` 抑制规则（用于关闭重复或冗余告警）

```yaml
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'instance']  # 如果两个告警 alertname 和 instance 相同，则 warning 会被抑制
```

---

## 6. `mute_time_intervals`（已弃用）

> 用于静音告警时间段，已被 `time_intervals` 替代。

---

## 7. `time_intervals` 设置告警激活/静音时间段

```yaml
time_intervals:
  - name: "night-hours"
    time_intervals:
      - times:
          - start_time: "00:00"
            end_time: "09:00"
        weekdays: ["monday", "tuesday", "wednesday", "thursday", "friday"]
```

---

