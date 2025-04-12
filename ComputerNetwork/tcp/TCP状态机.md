# TCP 状态机

## 1. TCP 状态机

TCP 状态机的作用是描述一个连接从开始到结束，可能经历的所有状态变化。经典 TCP 状态机大概有 **11 个状态**：

```
CLOSED → LISTEN → SYN_SENT → SYN_RECEIVED → ESTABLISHED
→ FIN_WAIT_1 → FIN_WAIT_2 → TIME_WAIT
→ CLOSE_WAIT → LAST_ACK
```


---

## 2. 状态详解(连接创建过程)

### 2.1 `CLOSED`：初始状态
- TCP 初始未连接时，处于 `CLOSED` 状态。
- 也可以是连接关闭后回到这个状态。

---

### 2.2 `LISTEN`（服务端监听状态）
- 服务端调用 `socket`、`bind`、`listen` 后，进入 `LISTEN`。
- 这时还未建立连接，等待接收客户端的 `SYN`。

---

### 2.3 `SYN_SENT`（客户端已发送 SYN）
- 客户端调用 `connect` 后，发出 SYN 包，进入此状态。
- 等待服务端回应 SYN+ACK。

---

### 2.4 `SYN_RECEIVED`（服务端收到 SYN，并回应 SYN+ACK）
- 服务端收到客户端 SYN 后，回复 SYN+ACK，进入 `SYN_RECEIVED`。
- 等待客户端再发一个 ACK 完成三次握手。

---

### 2.5 `ESTABLISHED`（连接建立）
- 三次握手完成后，双方都进入 `ESTABLISHED`。
- 这是正常通信状态，应用程序可以 `send/recv`。

---

## 3. 状态详解(连接关闭过程)

### 主动关闭一方（通常是客户端）：

### 3.1 `FIN_WAIT_1`
- 主动方调用 `close`，发出 FIN，进入 `FIN_WAIT_1`。

### 3.2 `FIN_WAIT_2`
- 收到对方 ACK 后，进入 `FIN_WAIT_2`。
- 等待对方发 FIN。

### 3.3 `TIME_WAIT`
- 收到对方 FIN 后，回复 ACK，进入 `TIME_WAIT`。
- 保持 2 倍 MSL（Maximum Segment Lifetime），确保最后的 ACK 被对方接收到。
- 然后进入 `CLOSED`。

---

### 被动关闭一方（通常是服务端）：

### 3.4 `CLOSE_WAIT`
- 被动方收到对方的 FIN 后，回复 ACK，进入 `CLOSE_WAIT`。
- 等待本地应用程序调用 `close`。

### 3.5 `LAST_ACK`
- 被动方调用 `close` 后，发出 FIN，进入 `LAST_ACK`。
- 等待对方的 ACK。

- 收到 ACK 后，进入 `CLOSED`。

---

## 4. TCP 状态图（简化版）

```text
Client:                        Server:

CLOSED                         LISTEN
   |                              |
connect() -> SYN_SENT        <- SYN_RECEIVED <- SYN
   |                              |
SYN+ACK <-                     SYN+ACK ->
   |                              |
ESTABLISHED <---------------> ESTABLISHED
   |                              |
FIN_WAIT_1  -> FIN -------------> CLOSE_WAIT
   |                              |
FIN_WAIT_2  <- ACK               (应用 close)
   |                              |
TIME_WAIT  <- FIN  <------------- LAST_ACK
   |         -> ACK               |
CLOSED   <------------------------ CLOSED
```

---

## 5. 重点状态分析

### `TIME_WAIT`
- 为什么要等 2MSL？
    - 确保对方收到最后的 ACK。
    - 防止旧连接数据影响后续连接。

### `CLOSE_WAIT` 很久不消失？
- 通常是**程序忘了调用 close()**，导致服务端 socket 半关闭卡死。

### `SYN_RECV` 多？
- 表示很多连接正在握手中但未完成，可能是：
    - 拒绝服务攻击（SYN flood）
    - 客户端网络不通
    - backlog 太小，握手排队溢出

---

## 6. 查看当前系统 TCP 状态

Linux 中可以用：
```bash

netstat -anp | grep tcp
```

