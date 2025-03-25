# rabbitmq-交换机

RabbitMQ 交换机（Exchange）是消息路由的核心组件，它决定了消息如何从生产者流向队列。

---

## 1. 交换机的作用
RabbitMQ 采用 **发布-订阅（Pub/Sub）模型**，其中 **交换机** 负责接收消息并按照一定的路由规则分发给 **队列（Queue）**。生产者（Producer）将消息发送到交换机，而交换机再根据绑定关系和路由键（Routing Key）决定消息的去向。

### **消息流转过程**

![exchange](./images/exchange.svg)

1. **生产者（Producer）** 将消息发送到指定的交换机。
2. **交换机（Exchange）** 根据自身类型和路由规则决定消息流向。
3. **队列（Queue）** 绑定到交换机，接收符合路由规则的消息。
4. **消费者（Consumer）** 从队列中消费消息。

---

## 2. 交换机的四种类型
RabbitMQ 提供了四种不同类型的交换机：
1. **Direct 交换机（直连交换机）**
2. **Fanout 交换机（扇出交换机）**
3. **Topic 交换机（主题交换机）**
4. **Headers 交换机（头交换机）**

### **2.1 Direct 交换机**
**特点**：
- 通过 **Routing Key（路由键）** 精确匹配队列的绑定键（Binding Key）。
- 只有绑定键匹配的队列才能接收到消息。

**适用场景**：
- 需要将消息精准投递到某个队列时，例如日志级别（info、warn、error）的分发。

**示例**：
假设有一个 `logs_direct` 交换机，有两个队列：
- `error_logs` 绑定键是 `"error"`
- `info_logs` 绑定键是 `"info"`

```plaintext
P ——> [logs_direct] ——> (error) ——> [error_logs]
                         (info)  ——> [info_logs]
```

当生产者发送一条 `Routing Key = "error"` 的消息，只有 `error_logs` 队列能接收该消息。

---

### **2.2 Fanout 交换机**
**特点**：
- **广播模式**，不管 Routing Key 是什么，消息都会发送到所有绑定的队列。
- 忽略 Routing Key。

**适用场景**：
- 需要将消息广播到多个消费者，例如：
  - 订阅发布系统（如新闻推送）。
  - 多个服务监听同一数据更新事件。

**示例**：
假设有一个 `logs_fanout` 交换机，绑定了多个队列：
- `queue_A`
- `queue_B`
- `queue_C`

```plaintext
P ——> [logs_fanout] ——> [queue_A]
                        ——> [queue_B]
                        ——> [queue_C]
```
无论生产者发送的 Routing Key 是什么，所有队列都能收到消息。

---

### **2.3 Topic 交换机**
**特点**：
- 通过 **通配符匹配** `Routing Key` 和 `Binding Key`。
- `Routing Key` 一般是 **"."（点）** 分隔的字符串，例如：`system.info.user`
- `Binding Key` 可以使用两个特殊通配符：
  - `*`：匹配 **一个** 词（单个分段）
  - `#`：匹配 **多个** 词（多个分段）

**适用场景**：
- 需要进行 **模式匹配**，如日志系统，按模块（auth、system）和级别（info、error）过滤日志。

**示例**：
假设有一个 `logs_topic` 交换机：
- `queue_A` 绑定键：`system.*`
- `queue_B` 绑定键：`#.error`

```plaintext
P ——> [logs_topic] ——> (system.*)   ——> [queue_A]
                        (#.error)   ——> [queue_B]
```

| 发送消息 `Routing Key` | `queue_A` | `queue_B` |
|--------------------|---------|---------|
| `system.info`     | ✅       | ❌       |
| `app.error`       | ❌       | ✅       |
| `system.error`    | ✅       | ✅       |

---

### **2.4 Headers 交换机**
**特点**：
- 通过 **消息头部（Headers）** 进行匹配，而不是 Routing Key。
- 可以指定 `x-match`：
  - `all`（默认）：所有指定头部必须匹配。
  - `any`：匹配任意一个头部即可。

**适用场景**：
- 适用于 **复杂匹配**，例如多条件筛选，如 "device=mobile" 且 "location=US"。

**示例**：
假设 `logs_headers` 交换机：
- `queue_A` 绑定头部 `{type: "error", format: "json"}`
- `queue_B` 绑定头部 `{type: "info"}`

```plaintext
P ——> [logs_headers] ——> {type: "error", format: "json"} ——> [queue_A]
                         ——> {type: "info"} ——> [queue_B]
```

如果生产者发送消息：
```json
{
  "headers": {
    "type": "error",
    "format": "json"
  }
}
```
那么 `queue_A` 会收到该消息。

---

## 3. 交换机绑定（Binding）
**绑定（Binding）** 是交换机和队列之间的 **关联关系**。当交换机收到消息时，会按照绑定规则把消息路由到相应的队列。

```plaintext
[Producer] --> [Exchange] --(Binding)--> [Queue]
```

绑定时可以指定：
- **Binding Key**（用于 Direct 和 Topic 交换机）
- **Headers 条件**（用于 Headers 交换机）

示例（Direct 交换机）：
```shell

# 创建交换机
rabbitmqadmin declare exchange name=logs_direct type=direct

# 创建队列
rabbitmqadmin declare queue name=error_logs

# 绑定队列
rabbitmqadmin declare binding source=logs_direct destination=error_logs routing_key=error
```

---

## 4. 交换机的持久化
- **持久化（Durable）**：交换机是否在 RabbitMQ 重启后仍然存在。
- **自动删除（Auto-Delete）**：如果所有绑定的队列都被删除，交换机是否自动删除。

```shell

rabbitmqadmin declare exchange name=logs_direct type=direct durable=true auto_delete=false
```

---

## 5. 交换机的高级特性
### **5.1 死信交换机（DLX, Dead Letter Exchange）**
当消息因 **TTL 过期**、**队列满了**、**被拒绝（rejected）** 时，可以将其路由到 **死信队列（DLQ）**。

配置示例：
```shell

rabbitmqadmin declare exchange name=dlx_exchange type=direct
rabbitmqadmin declare queue name=dlx_queue

# 绑定 DLX 到队列
rabbitmqadmin declare binding source=dlx_exchange destination=dlx_queue routing_key=dlx_key
```

然后在普通队列中指定 DLX：
```shell

rabbitmqadmin declare queue name=normal_queue arguments='{"x-dead-letter-exchange": "dlx_exchange", "x-dead-letter-routing-key": "dlx_key"}'
```

