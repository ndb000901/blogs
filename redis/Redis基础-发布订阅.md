# Redis基础-发布订阅


## 1. Redis 发布订阅（Pub/Sub）

Pub/Sub 是 Redis 提供的一种**消息通信机制**，基于**发布者-订阅者模型**，实现了**一对多的消息推送**。

> 发布者将消息发送到某个频道，所有订阅了该频道的客户端都会收到这条消息。

---

## 2. 场景举例

| 场景 | 说明 |
|------|------|
| 即时聊天系统 | 消息发送后，所有订阅者立刻收到 |
| 消息通知系统 | 订单状态变更、系统警报等 |
| 实时推送 | 实时行情、体育比分 |
| 微服务事件总线 | 服务之间通过 Pub/Sub 解耦通信 |

---

## 3. 常用命令

### 3.1 **订阅频道**

```bash

SUBSCRIBE channel [channel ...]
```

示例：

```bash

SUBSCRIBE news
```

客户端进入监听状态，收到消息时会实时输出。

---

### 3.2 **发布消息**

```bash

PUBLISH channel message
```

示例：

```bash

PUBLISH news "Hello subscribers!"
```

> 所有订阅了 `news` 的客户端将立即收到这条消息。

---

### 3.3 **取消订阅**

```bash

UNSUBSCRIBE [channel ...]
```

示例：

```bash

UNSUBSCRIBE news
```

---

### 3.4 **模式订阅**

使用通配符（模式）匹配多个频道。

```bash

PSUBSCRIBE pattern [pattern ...]
```

示例：

```bash

PSUBSCRIBE news.*
```

> 匹配所有形如 `news.*` 的频道，例如：`news.sports`、`news.weather`

取消方式：

```bash

PUNSUBSCRIBE [pattern ...]
```

---

## 4. 消息格式（客户端收到消息时）

### 普通订阅：

收到消息时的结构是一个数组：

```bash

1) "message"
2) "news"
3) "Hello subscribers!"
```

### 模式订阅：

```bash

1) "pmessage"
2) "news.*"
3) "news.sports"
4) "Sports update"
```

---

## 5. 特性与限制

| 特性/限制 | 说明 |
|----------|------|
| **无持久化** | 消息只发一次，不存储；离线用户不会收到 |
| **无确认机制** | 不保证消息一定被消费 |
| **消息广播** | 多个客户端可同时收到同一条消息 |
| **轻量实时** | 非阻塞，适用于实时性要求高的场景 |
| **不支持分区广播** | Pub/Sub 不支持按 key 分片（除非你在客户端分流）

---



