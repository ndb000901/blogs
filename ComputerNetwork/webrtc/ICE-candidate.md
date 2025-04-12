# ICE candidate

在 WebRTC 的 SDP 中，`a=candidate:` 行描述了该节点的候选网络地址（ICE candidate），这些候选地址可以被远端 peer 用来尝试建立连接。ICE 候选地址分为几种 **类型**，每种类型都有其特定的 **来源与适用性**。候选地址还包含一个 **优先级字段**，用于排序和选择连接路径。


## 1. Candidate 的类型

WebRTC 的 ICE 候选地址主要有以下几种类型：

| 类型      | 含义                        | 来源                   | 优缺点 |
|-----------|-----------------------------|------------------------|--------|
| `host`    | 本地地址（内网IP）           | 本机网卡               | 优：延迟低；缺：多数情况下NAT后无法直连 |
| `srflx`   | 服务器反射地址（公网IP）     | 通过 STUN 服务器获得   | 优：穿透单层NAT；缺：部分 NAT 会失败 |
| `prflx`   | peer reflexive（对端反射）    | 通信过程中动态推导获得 | 不常见，通常不主动生成 |
| `relay`   | 中继地址（TURN服务器提供）    | 通过 TURN 分配获得     | 优：万能连接；缺：带宽贵，延迟高 |


```sdp
a=candidate:foundation 1 udp 2130706431 192.168.1.1 53165 typ host ...
a=candidate:foundation 1 udp 1694498815 1.2.3.4 57336 typ srflx ...
```
说明此端支持局域网地址（host）和 STUN 公网地址（srflx）两类连接方式。

---

## 2. Candidate 的字段格式

每个 candidate 的格式如下：

```
a=candidate:<foundation> <component-id> <transport> <priority> <address> <port> typ <candidate-type> [raddr <raddr>] [rport <rport>]
```

例如：

```
a=candidate:foundation 1 udp 2130706431 192.168.1.1 53165 typ host generation 0
```

字段解析如下：

- `foundation`: 候选地址的唯一标识符，用于分组计算；
- `1`: 组件 ID，1 表示 RTP，2 表示 RTCP；
- `udp`: 传输协议；
- `2130706431`: 优先级（32位无符号数）；
- `192.168.1.1`: IP 地址；
- `53165`: 端口号；
- `typ host`: 地址类型；
- `generation 0`: ICE generation，可暂时忽略；
- `raddr/rport`: 对于 srflx/relay，指映射前的原地址；

---

## 3. Candidate 优先级计算

ICE Candidate 优先级是由浏览器或 WebRTC 实现计算得出，用于 ICE 候选排序。计算公式如下：

### [RFC 5245 定义的优先级计算公式]：

```
priority = (2^24) * type-preference +
           (2^8) * local-preference +
           (2^0) * (256 - component-id)
```

- `type-preference`: 不同类型的偏好值；
- `local-preference`: 同类型中选择优先的接口；
- `component-id`: 通常为 1（RTP）或 2（RTCP）；

### type-preference 的建议值：

| 类型      | type-preference |
|-----------|------------------|
| `host`    | 126              |
| `srflx`   | 100              |
| `relay`   | 0~90（一般 0 或 50）|


## 4. ICE 策略与排序行为

在连接建立时，WebRTC 的 ICE Agent 会：

1. 按照 candidate 优先级排序；
2. 发起 connect-check；
3. 最先成功建立连接的 pair 被选为 "nominated pair"（最终用于媒体传输）；
4. 如果一个 relay candidate 连接最快，也可能会选中 relay。

---

