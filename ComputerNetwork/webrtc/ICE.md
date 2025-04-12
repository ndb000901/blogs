# ICE

ICE 是 WebRTC 中用于**建立点对点连接**的协议框架，目的是：

> **在各种网络环境下（尤其是 NAT 存在时），找出一对可用的 IP 地址和端口，使两个端能成功通信。**

它本身不是一个协议，而是一个框架，集成了：
- STUN（打洞）
- TURN（中继）
- 地址优选策略
- 连接检测（Connectivity Checks）

---

## 1. ICE 候选类型（Candidates）
ICE 会收集多个“候选地址（Candidate）”，每个候选就是一个“可能可以连通的地址:端口组合”。

| 类型 | 名称 | 描述 |
|------|------|------|
| host | 本地地址 | 网卡的局域网 IP（如 `192.168.x.x`） |
| srflx | Server Reflexive | STUN server 看到的公网 IP |
| relay | 中继地址 | 通过 TURN server 分配的地址 |
| prflx | Peer Reflexive | 对端通过检测获得的候选 |

---

## 2. ICE 连接建立流程

以下是 WebRTC 中 ICE 的典型完整流程：

### 2.1 **候选收集（Gathering Phase）**
- 获取本地 IP（host）
- 通过 STUN 获取 srflx 候选（即公网 IP:port）
- 如果配置了 TURN，向其申请 relay 候选（中继）

> 结果是：每端收集了多个候选地址（host + srflx + relay）

---

### 2.2 **候选交换（Signaling Phase）**
通过信令（WebSocket / HTTP / SIP 等）交换 SDP，里面包含了 ICE 候选。

---

### 2.3 **连接检测（Connectivity Checks）**
ICE 会做这样的检测流程：

- 双方组合出所有可能的候选对（Candidate Pair）
    - 举例：A 的 host + B 的 srflx，A 的 srflx + B 的 relay ...
- 每个 Pair 会通过 **STUN Binding Request** 检测连接性
- 若 STUN 成功响应，说明这个候选对是 **可用的**

---

### 2.4 **优选路径（Nomination）**
- 根据优先级（host > srflx > relay）选择最佳连接路径
- 通常优先选低延迟、无中继的路径（比如：内网直接连）

---

### 2.5 **连接锁定（Connection Established）**
- 确定哪一对候选作为最终连接（nominated pair）
- 后续媒体（RTP）或数据（SCTP）流量通过这个路径传输

---

## 3. ICE Candidate 的结构
一个典型 SDP 中的 ICE 候选长这样：

```
a=candidate:842163049 1 udp 1677729535 192.168.1.2 50437 typ host
a=candidate:2337205803 1 udp 1845501695 203.0.113.1 53705 typ srflx raddr 192.168.1.2 rport 50437
```

字段解释：

| 字段 | 含义 |
|------|------|
| `candidate:` | 开头 |
| `842163049` | foundation |
| `1` | component ID（1=RTP，2=RTCP） |
| `udp` | 协议 |
| `192.168.1.2 50437` | IP:port |
| `typ host/srflx/relay` | 类型 |

---

## 4. ICE 状态机（在 WebRTC 中）
常见状态有：

| 状态 | 描述 |
|------|------|
| `new` | ICE 尚未开始 |
| `gathering` | 正在收集候选 |
| `checking` | 正在做 connectivity checks |
| `connected` | 找到了至少一个连通路径 |
| `completed` | 已选定路径并打通 |
| `disconnected` | 检测不到连接 |
| `failed` | 所有候选对都失败了 |

---


