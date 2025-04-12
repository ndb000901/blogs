# RTCP 协议

## 1. RTCP

**RTCP（实时传输控制协议）** 是 RTP 的姊妹协议，用于对 RTP 媒体传输进行 **控制、统计和反馈**。

- 全称：Real-time Transport Control Protocol
- RTP 传输媒体流，RTCP 传输 **统计信息、控制信息、网络反馈**
- 和 RTP 使用同样的底层传输（通常是 UDP）

---

## 2. RTCP 的设计目的


| 目标 | 描述 |
|------|------|
| QoS 反馈 | 提供丢包率、抖动、延迟等网络质量统计 |
| 同步 | 用于同步音视频流（配合 RTP 时间戳） |
| 标识 | 提供参与者名称、邮箱、CNAME 等信息 |
| 控制 | 提供流状态，如 BYE 通知离开、NACK 重传等 |
| 适配 | 根据接收反馈动态调节发送码率（拥塞控制）|

---

## 3. RTCP 报文结构（固定头）

RTCP 报文具有统一的头部格式，结构如下：

```
  0                   1                   2                   3
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |V=2|P|  RC   |      PT         |           length              |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### 3.1 字段说明：

| 字段 | 含义 |
|------|------|
| **V** | RTP 协议版本，始终为 `2` |
| **P** | Padding（是否填充） |
| **RC** | Report Count，表示跟随报告的个数 |
| **PT** | Packet Type（报文类型） |
| **length** | 本 RTCP 包的长度，以 32 位字为单位，**不含头部自身** |

---

### 3.2 常见 RTCP 报文类型（PT）

| PT 值 | 名称 | 作用 |
|-------|------|------|
| 200   | **SR** (Sender Report) | 发送者报告：带 RTP 时间戳与发送统计 |
| 201   | **RR** (Receiver Report) | 接收者报告：反馈丢包、延迟、抖动等 |
| 202   | **SDES** (Source Description) | 说明发送源，如 CNAME、用户名 |
| 203   | **BYE** | 通知离开会议 |
| 204   | **APP** | 应用自定义扩展 |
| 205   | **RTPFB** | RTP 反馈，如 NACK（丢包反馈） |
| 206   | **PSFB** | Picture Loss Indication 等视频相关反馈 |

---

#### 3.2.1 RTCP Sender Report (SR) 详解（PT=200）

用于 RTP 媒体流发送者，提供统计信息（包数、字节数）以及用于同步的时间戳。

| 字段 | 描述 |
|------|------|
| NTP 时间戳 | 64-bit，绝对时间，用于音视频同步 |
| RTP 时间戳 | 32-bit，映射到 RTP 流的时间戳 |
| Packet count | 发送的 RTP 包数量 |
| Octet count | 发送的总字节数 |

---

#### 3.2.2 RTCP Receiver Report (RR) 详解（PT=201）

用于接收方，反馈 RTP 接收情况，最多可包含 31 个 Report Block：

| 字段 | 描述 |
|------|------|
| Fraction lost | 0~255，最近报告周期内丢失的比例 |
| Cumulative lost | 累计丢包数 |
| Highest Seq | 已接收到的最高 RTP 序列号 |
| Jitter | 抖动 |
| LSR/DLSR | 上次 SR 报告时间与当前延迟 |

---

#### 3.2.3 RTCP SDES（Source Description）详解（PT=202）

提供媒体源的标识信息，如：

- CNAME（Canonical Name）用于区分媒体源
- NAME（用户名称）
- EMAIL、LOC、TOOL 等

这是用于多媒体同步和标识的重要信息。

---

#### 3.2.4 RTCP BYE（PT=203）

通知接收端：“我要走了”。例如：

- 用户离开会议
- 会话关闭
- 音视频流断开

---

#### 3.2.5 RTCP RTPFB / PSFB（PT=205/206）

这是 WebRTC 中关键的反馈机制，称为 **RTCP XR（扩展反馈）**：

### RTPFB 反馈类型：
- **NACK（1）**：请求重传丢失的 RTP 包
- **TWCC（15）**：Transport Wide Congestion Control，用于拥塞控制

### PSFB 反馈类型：
- **PLI（1）**：Picture Loss Indication，请求关键帧
- **FIR（4）**：Full Intra Request，强制 I 帧请求
- **REMB**（Google 自定义）：Receiver Estimated Max Bitrate，用于带宽估计

---

## 4. RTP 与 RTCP 协同流程图

```
[RTP Sender]  ------------------- RTP -------------------> [Receiver]
      |                                                     |
      | <------------------- RTCP RR (质量反馈) ------------|
      | <------------------- RTCP PLI / REMB ---------------|
      | -------------------- RTCP SR ---------------------> |
```

---


## 5. 对比：RTP vs RTCP

| 特性 | RTP | RTCP |
|------|-----|------|
| 作用 | 传输音视频 | 控制、反馈 |
| 数据 | 实时媒体帧 | 报告、控制信息 |
| 可靠性 | 不可靠（UDP） | 不可靠，但容忍 |
| 加密 | SRTP | SRTCP |
| 常见类型 | 无（靠 Payload） | SR、RR、SDES、BYE、PLI、NACK 等 |
| 在 WebRTC 中 | 承载 Opus/VP8 等流 | 控制帧（如关键帧请求、重传反馈） |

---

