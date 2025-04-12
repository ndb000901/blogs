# 会话描述协议 SDP

## 1. SDP

**SDP（Session Description Protocol）** 是一种用于描述多媒体会话的格式化文本协议，用于在通信双方之间协商音视频参数、编解码器、网络地址、媒体方向等信息。

> 在 WebRTC 中，SDP 是信令的核心内容，是 ICE、DTLS、SRTP、编解码器、音视频轨道等的“元信息”。

---

## 2. SDP 格式总览

SDP 是一种文本格式，由一系列以特定前缀开头的行组成，例如：

```sdp
v=0
o=- 3897747309 2 IN IP4 127.0.0.1
s=-
t=0 0
a=group:BUNDLE 0 1
a=msid-semantic: WMS

m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104
c=IN IP4 0.0.0.0
a=rtcp:9 IN IP4 0.0.0.0
a=mid:0
a=sendrecv
a=rtpmap:111 opus/48000/2
...

m=video 9 UDP/TLS/RTP/SAVPF 96 97
a=mid:1
a=rtpmap:96 VP8/90000
```

SDP 分为 **Session-level** 和 **Media-level** 两部分，分别描述全局和每个媒体轨的信息。

---

## 3. 字段详解

| 行前缀 | 含义 | 示例 |
|--------|------|------|
| `v=` | SDP 版本 | `v=0` |
| `o=` | 会话所有者/唯一ID | `o=- 3897747309 2 IN IP4 127.0.0.1` |
| `s=` | 会话名称 | `s=-` |
| `t=` | 时间范围 | `t=0 0`（表示无限期） |
| `a=group` | 媒体协商关系（BUNDLE等） | `a=group:BUNDLE 0 1` |
| `m=` | 媒体描述（媒体类型、端口、传输协议、payload） | `m=audio 9 UDP/TLS/RTP/SAVPF 111` |
| `c=` | 连接信息 | `c=IN IP4 0.0.0.0` |
| `a=rtpmap` | payload 映射（编号与编码器的映射） | `a=rtpmap:111 opus/48000/2` |
| `a=fmtp` | 编解码器参数 | `a=fmtp:111 minptime=10; useinbandfec=1` |
| `a=setup` | DTLS 协议角色 | `a=setup:actpass` |
| `a=ice-ufrag`, `a=ice-pwd` | ICE身份验证 | 用于 STUN/TURN |
| `a=candidate` | ICE候选地址 | `a=candidate:...` |
| `a=mid` | 媒体流 ID，用于 bundle | `a=mid:0` |
| `a=sendrecv` | 媒体方向 | `sendonly`, `recvonly`, `inactive` |

---


## 4. SDP 中的重点说明（WebRTC 语义）

### 编解码器

```sdp
m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104
a=rtpmap:111 opus/48000/2
a=fmtp:111 minptime=10; useinbandfec=1
```

- `opus/48000/2`：OPUS 编码，48kHz，双通道；
- `fmtp`：格式参数（minptime, fec等）；

---

### ICE + DTLS + SRTP

```sdp
a=ice-ufrag:abcd
a=ice-pwd:efghijkl
a=fingerprint:sha-256 AA:BB:CC:...
a=setup:actpass
a=rtcp-mux
```

- `fingerprint`：用于 DTLS 握手；
- `ice-ufrag`/`pwd`：用于 NAT 穿透认证；
- `setup:actpass`：被动等待对方建立 DTLS；

---

### 多路复用（BUNDLE）

```sdp
a=group:BUNDLE 0 1
```

- WebRTC 默认将音视频、数据等媒体复用在一个 5元组连接（端口）上；
- BUNDLE 用 `a=mid:` 来绑定每个媒体；

---

### SSRC 标识流

```sdp
a=ssrc:12345678 cname:mycname
a=msid:streamId trackId
```

- 标识 RTP 流；
- 可以用于标识视频轨道、音频轨道是否重用、静音等；

---

## 5. SDP示例

```text

v=0
o=- 3546004397921447048 1596742744 IN IP4 0.0.0.0
s=-
t=0 0
a=fingerprint:sha-256 0F:74:31:25:CB:A2:13:EC:28:6F:6D:2C:61:FF:5D:C2:BC:B9:DB:3D:98:14:8D:1A:BB:EA:33:0C:A4:60:A8:8E
a=group:BUNDLE 0 1
m=audio 9 UDP/TLS/RTP/SAVPF 111
c=IN IP4 0.0.0.0
a=setup:active
a=mid:0
a=ice-ufrag:CsxzEWmoKpJyscFj
a=ice-pwd:mktpbhgREmjEwUFSIJyPINPUhgDqJlSd
a=rtcp-mux
a=rtcp-rsize
a=rtpmap:111 opus/48000/2
a=fmtp:111 minptime=10;useinbandfec=1
a=ssrc:350842737 cname:yvKPspsHcYcwGFTw
a=ssrc:350842737 msid:yvKPspsHcYcwGFTw DfQnKjQQuwceLFdV
a=ssrc:350842737 mslabel:yvKPspsHcYcwGFTw
a=ssrc:350842737 label:DfQnKjQQuwceLFdV
a=msid:yvKPspsHcYcwGFTw DfQnKjQQuwceLFdV
a=sendrecv
a=candidate:foundation 1 udp 2130706431 192.168.1.1 53165 typ host generation 0
a=candidate:foundation 2 udp 2130706431 192.168.1.1 53165 typ host generation 0
a=candidate:foundation 1 udp 1694498815 1.2.3.4 57336 typ srflx raddr 0.0.0.0 rport 57336 generation 0
a=candidate:foundation 2 udp 1694498815 1.2.3.4 57336 typ srflx raddr 0.0.0.0 rport 57336 generation 0
a=end-of-candidates
m=video 9 UDP/TLS/RTP/SAVPF 96
c=IN IP4 0.0.0.0
a=setup:active
a=mid:1
a=ice-ufrag:CsxzEWmoKpJyscFj
a=ice-pwd:mktpbhgREmjEwUFSIJyPINPUhgDqJlSd
a=rtcp-mux
a=rtcp-rsize
a=rtpmap:96 VP8/90000
a=ssrc:2180035812 cname:XHbOTNRFnLtesHwJ
a=ssrc:2180035812 msid:XHbOTNRFnLtesHwJ JgtwEhBWNEiOnhuW
a=ssrc:2180035812 mslabel:XHbOTNRFnLtesHwJ
a=ssrc:2180035812 label:JgtwEhBWNEiOnhuW
a=msid:XHbOTNRFnLtesHwJ JgtwEhBWNEiOnhuW
a=sendrecv
```

### 会话级别（Session-level）字段

```
v=0
```
- SDP 版本号，这里是 `0`，当前唯一的有效值。

```
o=- 3546004397921447048 1596742744 IN IP4 0.0.0.0
```
- o= 是 "origin"（发起者）字段。
    - `-`: username（匿名）
    - `3546004397921447048`: session ID（唯一标识本次会话）
    - `1596742744`: session version（每次变更 SDP 时都要递增）
    - `IN IP4 0.0.0.0`: 网络类型和地址，这里是 IPv4，地址未知或无效（WebRTC 常见）

```
s=-
```
- 会话名称，这里设为 `-`，表示无。

```
t=0 0
```
- 会话时间，这里为 0 0，表示会话“永久有效”（用于 WebRTC）

```
a=fingerprint:sha-256 0F:74:31:25:CB:A2:13:EC:28:6F:6D:2C:61:FF:5D:C2:BC:B9:DB:3D:98:14:8D:1A:BB:EA:33:0C:A4:60:A8:8E
```
- 用于 DTLS 握手验证的证书指纹，sha-256 是算法。

```
a=group:BUNDLE 0 1
```
- `BUNDLE` 是 WebRTC 中将多个 `m=` 媒体流共享一个传输通道（即复用 ICE 连接）的机制。
- `0 1` 是后面两个 media 的 `mid` 标识符，表明音视频都用一套通道。

---

### 音频（audio）媒体描述部分

```
m=audio 9 UDP/TLS/RTP/SAVPF 111
```
- `m=` 媒体描述：
    - `audio`：媒体类型是音频；
    - `9`：端口号，WebRTC 中通常固定为 `9`（ICE 协商后决定实际端口）；
    - `UDP/TLS/RTP/SAVPF`：传输协议，RTP over UDP，支持 SRTP；
    - `111`：动态 payload 类型 ID，后面会映射为具体 codec。

```
c=IN IP4 0.0.0.0
```
- 媒体连接地址，这里是 `0.0.0.0`，表示暂时无地址（由 ICE 决定）

```
a=setup:active
```
- `DTLS` 协议中，active 表示这个端是客户端角色（主动连接）。

```
a=mid:0
```
- 媒体流的标识符 `0`，与 `a=group:BUNDLE 0 1` 对应。

```
a=ice-ufrag:CsxzEWmoKpJyscFj
a=ice-pwd:mktpbhgREmjEwUFSIJyPINPUhgDqJlSd
```
- ICE（Interactive Connectivity Establishment）用于 NAT 穿透的用户名和密码。

```
a=rtcp-mux
```
- 表示 RTP 和 RTCP 都用同一个端口。

```
a=rtcp-rsize
```
- 启用 Reduced Size RTCP（压缩 RTCP 包头部）

```
a=rtpmap:111 opus/48000/2
```
- 将 payload type 111 映射为 Opus 编码，采样率 48kHz，2 声道。

```
a=fmtp:111 minptime=10;useinbandfec=1
```
- Opus 的 codec 参数：
    - `minptime=10`：最小包时长为 10ms；
    - `useinbandfec=1`：启用内置的 FEC（前向纠错）。

```
a=ssrc:350842737 cname:yvKPspsHcYcwGFTw
...
a=msid:yvKPspsHcYcwGFTw DfQnKjQQuwceLFdV
```
- `ssrc` 是同步源 ID（唯一标识一个流）；
- `cname` 是 RTCP 用于跨流同步的标识符；
- `msid`/`label`/`mslabel` 是媒体流标识，与 HTML5 中的 MediaStream API 对应。

```
a=sendrecv
```
- 该端即发送又接收（双向）。

```
a=candidate:...
```
- ICE 候选地址：
    - `foundation`: 候选基础名；
    - `1` 或 `2`: component ID；
    - `udp`: 协议；
    - `2130706431`：优先级；
    - `192.168.1.1 53165`: 内网 IP 和端口；
    - `typ host`: 类型是主机地址；
    - `typ srflx`: 服务器反射地址（公网地址）；
    - `generation`: ICE generation（版本号）；
    - `raddr/rport`: 映射关系。

```
a=end-of-candidates
```
- 表示候选地址发送完毕。

---

### 视频（video）媒体描述部分

```
m=video 9 UDP/TLS/RTP/SAVPF 96
```
- `video` 类型媒体；
- `96` 是动态 payload type；

```
a=rtpmap:96 VP8/90000
```
- 将 96 映射为 VP8 编码，时钟率 90kHz。

```
a=ssrc:2180035812 ...
a=msid:XHbOTNRFnLtesHwJ JgtwEhBWNEiOnhuW
```
- 与音频一样，提供 video 的 SSRC、cname、msid、label 等。

```
a=sendrecv
```
- 视频也是双向。

---


