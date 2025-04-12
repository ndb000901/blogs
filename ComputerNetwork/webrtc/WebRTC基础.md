# WebRTC 基础

WebRTC 是一个高度集成的实时通信框架，内部涉及多个协议栈，覆盖从信令到传输、媒体编解码的整个链路。下面是 WebRTC 涉及的主要协议：

---

### **1. 信令相关（Signaling Layer）**
WebRTC **本身不定义信令协议**，但常用的协议包括：
- **WebSocket** / **HTTP(S)**：用于信令通信

信令主要用于交换：
- SDP（Session Description Protocol）：媒体协商描述
- ICE candidate（候选网络地址）

---

### **2. NAT 穿透与连接建立（ICE 框架）**
- **ICE (Interactive Connectivity Establishment)**：连接建立框架
- **STUN (Session Traversal Utilities for NAT)**：用于发现公网地址
- **TURN (Traversal Using Relays around NAT)**：用于 NAT 无法打洞时中继数据

---

### **3. 传输层**
- **UDP**（首选）：低延迟
- **TCP**（回退）：某些网络条件下的 fallback
- **DTLS (Datagram Transport Layer Security)**：用于加密数据通道和媒体流（SRTP 密钥协商）
- **SRTP (Secure Real-time Transport Protocol)**：加密 RTP 媒体流
- **ICE-TCP**：TURN over TCP 的场景

---

### **4. 媒体传输协议**
- **RTP (Real-time Transport Protocol)**：传输音视频
- **RTCP (RTP Control Protocol)**：统计/控制信息，如丢包、延迟、带宽估算等
- **RTCPeerConnection**：WebRTC 抽象 API，但背后跑的是 RTP/RTCP

---

### **5. 数据通道协议**
- **SCTP (Stream Control Transmission Protocol)**：用于 WebRTC DataChannel（数据通道）
- **DTLS over SCTP**：数据通道加密

---

### **6. 媒体协商协议**
- **SDP (Session Description Protocol)**：描述音视频格式、编解码器、ICE 候选、DTLS 信息等

---

### 补充（协议不是 WebRTC 独有，但被 WebRTC 使用）
- **Codec 协议**：如 VP8/VP9/H264（视频），Opus/G.711（音频）
- **RTCP Feedback 消息**：如 NACK、PLI、REMB、TWCC（用于带宽估算、重传、请求关键帧）

