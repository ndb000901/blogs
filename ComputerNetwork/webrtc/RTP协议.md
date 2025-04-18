# RTP 协议

## 1. RTP

**RTP（Real-time Transport Protocol）** 是一种用于在 IP 网络中传输**音频、视频或其他实时数据**的协议。

- RTP 本身 **不保证传输可靠性（不是 TCP）**；
- 通常使用 **UDP** 作为底层传输层协议；
- 主要用于 **实时应用**：VoIP、视频会议、直播、WebRTC 等；
- 和 **RTCP**（Real-time Transport Control Protocol）配合使用：用于统计、控制、同步等。

---

## 2. RTP 报文结构

RTP 数据包头部固定为 **12 字节**，其结构如下：

```
  0                   1                   2                   3
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |V=2|P|X|  CC   |M|     PT      |       sequence number         |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                           timestamp                           |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |           synchronization source (SSRC) identifier            |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |            contributing source (CSRC) identifiers             |
 |                             ....                              |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### 字段说明：

| 字段名 | 位数 | 含义 |
|--------|------|------|
| **V**（Version） | 2 | RTP协议版本，当前为 2 |
| **P**（Padding） | 1 | 是否包含填充字节，最后几个字节是填充 |
| **X**（Extension） | 1 | 是否包含扩展头 |
| **CC**（CSRC Count） | 4 | 后面 CSRC 的数量（最多 15 个） |
| **M**（Marker） | 1 | 标记位，常用于帧边界（如视频的关键帧） |
| **PT**（Payload Type） | 7 | 有效负载类型，标识使用的编解码（如 Opus = 111） |
| **Sequence Number** | 16 | RTP 序列号，每个包加 1，用于重排和丢包检测 |
| **Timestamp** | 32 | 时间戳，表示采样时间（用于同步） |
| **SSRC** | 32 | 同步源唯一标识（一个音视频流一个 SSRC） |
| **CSRC** | 可选 | 混音时的子源 ID |


### RTP Payload Type （常见编码）

| Payload Type | 编解码类型 | 描述 |
|--------------|------------|------|
| 0            | PCMU       | G.711 μ-law 音频 |
| 8            | PCMA       | G.711 A-law 音频 |
| 96-127       | 动态定义   | 常用于 VP8、H264、Opus |
| 111          | Opus       | WebRTC 默认音频 |
| 100-120      | VP8/VP9/H264 等 | 视频流编码 |
---

## 3.RTP 设计目标

- 低延迟（用于实时通信）；
- 支持时间同步（通过时间戳）；
- 可用于流的识别（SSRC）；
- 便于丢包检测（序列号）；
- 可扩展（支持扩展头）；
- 搭配 RTCP 获取统计和质量控制信息。

---

## 4. RTP 和 RTCP 的配合使用

| 协议 | 功能 |
|------|------|
| RTP  | 传输实时媒体（音频/视频） |
| RTCP | 周期性发送控制包（统计、质量反馈） |

RTCP 提供如下信息：
- 丢包率、抖动、延迟等；
- 接收端对发送端反馈网络状况；


---

## 5. SRTP

在 WebRTC 中 RTP 数据包通过 **SRTP（Secure RTP）** 加密传输：

- 结合 **DTLS-SRTP** 密钥协商机制；
- 确保媒体内容的机密性、完整性和身份认证。

---







