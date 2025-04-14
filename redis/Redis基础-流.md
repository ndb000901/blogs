# Redis基础-流

## 1. Redis Stream

`Stream` 是 Redis 5.0 引入的一种**可持久化的消息队列型数据结构**，适合实现消息流（message stream）、日志系统、发布订阅、任务队列等场景。类似于 Kafka、RabbitMQ，但功能更轻量。

底层本质上是一个**可追加、可查询的双层压缩列表（listpack）**，具备消息持久化、消费组、阻塞读等特性。

---

## 2. Stream 基本结构

每条消息由：

- **唯一 ID**（timestamp-sequence，比如 `1689307458361-0`）
- **键值对字段列表（field-value）**

组成。

### 示例：

```bash

XADD mystream * sensor-id 1234 temperature 19.8
```

插入消息到 `mystream` 流，`*` 让 Redis 自动生成唯一 ID。

---

## 3. 常用命令

### 3.1 `XADD`：添加消息到流

```bash

XADD mystream * field1 value1 [field2 value2 ...]
```

- `*`：自动生成 ID（推荐）
- `MAXLEN`：控制最大长度，避免过大

```bash

XADD mystream MAXLEN 1000 * user alice msg "hi"
```

> 添加时，支持设置最大长度（近似修剪）

---

### 3.2 `XRANGE`：正序读取消息

```bash

XRANGE mystream start end [COUNT n]
```

```bash

XRANGE mystream - +
```

- `-`：最小 ID，`+`：最大 ID
- 支持分页读取历史记录

---

### 3.3 `XREVRANGE`：逆序读取消息

```bash

XREVRANGE mystream + - [COUNT n]
```

---

### 3.4 `XREAD`：阻塞式读取新消息（消费者）

```bash

XREAD BLOCK 0 STREAMS mystream $
```

- `$` 表示“从最新消息开始”
- `BLOCK 0` 表示阻塞直到有新消息（可设超时）

---

### 3.5 `XDEL`：删除消息

```bash

XDEL mystream 1689307458361-0
```

但 Stream 的结构仍然保留消息的“洞”。

---

### 3.6 `XLEN`：获取流长度

```bash

XLEN mystream
```

---

## 4. 消费者组（Consumer Groups）

Redis Stream 支持类似 Kafka 的**消费组模型**，用于**多消费者协作处理消息**，适合构建分布式系统。

### 消费组命令：

### 4.1 `XGROUP CREATE`：创建消费组

```bash

XGROUP CREATE mystream mygroup $ MKSTREAM
```

- `$` 表示从最后一个 ID 开始消费
- `MKSTREAM`：如果流不存在则创建

### 4.2 `XREADGROUP`：组内消费

```bash

XREADGROUP GROUP mygroup consumer-1 BLOCK 2000 STREAMS mystream >
```

- `>` 表示从未被该组消费的消息开始读取
- 支持阻塞读取

### 4.3 `XACK`：确认消息已处理完

```bash

XACK mystream mygroup 1689307458361-0
```

- 非确认的消息称为 PEL（Pending Entry List）

### 4.4 `XPENDING`：查看待确认消息

```bash

XPENDING mystream mygroup
```

---

## 5. 消息处理流程

1. 生产者用 `XADD` 写入流
2. 消费组内多个消费者用 `XREADGROUP` 拉取数据
3. 每个消息由一个消费者处理
4. 成功后 `XACK` 确认处理完毕

---

