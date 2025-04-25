# Alertmanager 通知方式


## 1. Alertmanager 支持的通知方式

| 类型             | 描述 |
|------------------|------|
| **Email**        | 发送邮件通知 |
| **Slack**        | 发送 Slack 消息 |
| **Webhook**      | 向自定义 HTTP 服务发送 JSON 格式的告警 |
| **PagerDuty**    | 向 PagerDuty 发出事件触发 |
| **Opsgenie**     | 集成 Opsgenie 平台告警 |
| **VictorOps**    | （现为 Splunk On-Call）告警通知 |
| **Pushover**     | 推送消息到 Pushover 应用（如手机 App） |
| **WeChat 企业微信** | 支持通过企业微信机器人发送消息 |
| **Telegram**     | 通过 Telegram Bot 发送通知（需 webhook 转发） |
| **Discord**      | 类似 Slack，通过 webhook 集成 |
| **SNS (Amazon Simple Notification Service)** | 通过 Webhook 可间接接入 |
| **Custom webhook** | 支持你自己实现通知逻辑（如调用飞书、钉钉等） |

---


### 1.1 Email 示例

```yaml
receivers:
  - name: "mail"
    email_configs:
      - to: "you@example.com"
        from: "alertmanager@example.com"
        smarthost: "smtp.example.com:587"
        auth_username: "alertmanager@example.com"
        auth_password: "password"
```

---

### 1.2 Slack 示例

```yaml
receivers:
  - name: "slack"
    slack_configs:
      - api_url: "https://hooks.slack.com/services/your/webhook/url"
        channel: "#alerts"
```

---

### 1.3 Webhook 示例（自定义转发飞书/钉钉）

```yaml
receivers:
  - name: "webhook"
    webhook_configs:
      - url: "http://localhost:5001/alert"
```

可以通过 Python/Node.js 等语言实现一个简单的 webhook 接收器，然后转发到飞书、钉钉、短信、微信等。

---

### 1.4 企业微信示例（通过 Webhook 机器人）

```yaml
receivers:
  - name: "wechat"
    webhook_configs:
      - url: "https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx"
```

---

