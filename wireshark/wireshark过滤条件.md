Wireshark 的过滤器分为 **显示过滤器（Display Filters）** 和 **捕获过滤器（Capture Filters）**。两者作用不同，语法也有差别。下面详细解析两种过滤器的使用方式及常见过滤条件。

---

## 1. **显示过滤器（Display Filters）**
**作用**：用于分析已经捕获的流量，动态调整 Wireshark 界面显示的内容。  
**语法**：基于 **Wireshark 自有语法**，支持丰富的协议字段匹配和逻辑运算。

### **基本语法**
- `协议`
- `字段 = 值`
- `字段 运算符 值`
- 逻辑运算：`&&`（与），`||`（或），`!`（非）

### **常见过滤条件**
| 过滤条件 | 作用 |
|----------|------|
| `ip.addr == 192.168.1.1` | 过滤 IP 地址为 192.168.1.1 的流量（源或目标） |
| `ip.src == 192.168.1.1` | 仅显示源 IP 为 192.168.1.1 的数据包 |
| `ip.dst == 192.168.1.2` | 仅显示目标 IP 为 192.168.1.2 的数据包 |
| `tcp.port == 80` | 过滤 HTTP 流量（HTTP 默认端口 80） |
| `tcp.dstport == 443` | 过滤目标端口为 443（HTTPS）的流量 |
| `udp.port == 53` | 过滤 DNS 查询流量（DNS 默认 UDP 端口 53） |
| `eth.addr == 00:1A:2B:3C:4D:5E` | 过滤指定 MAC 地址的数据包 |
| `frame contains "password"` | 查找数据包中包含 "password" 字符串的流量 |
| `tcp.flags.syn == 1` | 仅显示 TCP 三次握手中的 SYN 包 |
| `http.request.method == "POST"` | 仅显示 HTTP POST 请求 |
| `http.host contains "example.com"` | 仅显示请求的主机名包含 "example.com" 的 HTTP 流量 |
| `!(arp || icmp)` | 过滤掉 ARP 和 ICMP 数据包 |

### **组合使用**
```plaintext
(ip.src == 192.168.1.1 || ip.dst == 192.168.1.1) && tcp.port == 80
```
**解释**：显示所有 **源 IP 或目标 IP 为 192.168.1.1**，且 **端口号为 80** 的 TCP 流量。

---

## 2. **捕获过滤器（Capture Filters）**
**作用**：在流量捕获时生效，仅捕获符合条件的数据包，减少数据量。  
**语法**：基于 **BPF（Berkeley Packet Filter）语法**，功能较显示过滤器简单，支持 IP、端口、协议等。

### **基本语法**
- `协议`
- `host IP`
- `net 网络地址`
- `port 端口号`
- 逻辑运算：`and`（与），`or`（或），`not`（非）

### **常见过滤条件**
| 过滤条件 | 作用 |
|----------|------|
| `host 192.168.1.1` | 仅捕获与 192.168.1.1 相关的数据包 |
| `src host 192.168.1.1` | 仅捕获源 IP 为 192.168.1.1 的数据包 |
| `dst host 192.168.1.2` | 仅捕获目标 IP 为 192.168.1.2 的数据包 |
| `net 192.168.1.0/24` | 仅捕获 192.168.1.0/24 网段的流量 |
| `port 443` | 仅捕获 HTTPS（443端口）流量 |
| `tcp port 22` | 仅捕获 SSH（22端口）流量 |
| `udp port 53` | 仅捕获 DNS 查询流量 |
| `tcp` | 仅捕获 TCP 流量 |
| `udp` | 仅捕获 UDP 流量 |
| `icmp` | 仅捕获 ICMP（ping）流量 |
| `arp` | 仅捕获 ARP 请求 |
| `not arp` | 过滤掉 ARP 数据包 |
| `(host 192.168.1.1 and port 80) or (host 192.168.1.2 and port 443)` | 仅捕获 **192.168.1.1 的 HTTP（80）流量** 或 **192.168.1.2 的 HTTPS（443）流量** |

---

## 3. **显示过滤器 vs. 捕获过滤器**
| 过滤器类型 | 作用 | 语法 |
|------------|------|------|
| **显示过滤器** | 过滤 **已捕获** 的数据包，适用于分析流量 | Wireshark 语法（支持协议字段） |
| **捕获过滤器** | 过滤 **正在捕获** 的数据包，仅存储符合条件的数据 | BPF 语法（支持基本 IP、端口、协议） |

---

## 4. **实践案例**
### **示例 1：分析 HTTP 流量**
捕获过滤器：
```plaintext
tcp port 80
```
**解释**：仅捕获 HTTP（端口 80）的数据包，避免存储过多数据。

显示过滤器：
```plaintext
http.request || http.response
```
**解释**：在已捕获的流量中，仅显示 HTTP 请求和响应。

---

### **示例 2：抓取特定设备的 DNS 查询**
捕获过滤器：
```plaintext
host 192.168.1.100 and udp port 53
```
**解释**：仅捕获 IP 为 `192.168.1.100` 的设备发出的 DNS 查询。

显示过滤器：
```plaintext
dns
```
**解释**：在已捕获的流量中，仅显示 DNS 相关的包。

---

### **示例 3：分析 TCP 三次握手**
显示过滤器：
```plaintext
tcp.flags.syn == 1 || tcp.flags.ack == 1
```
**解释**：筛选 TCP SYN 和 ACK 数据包，便于分析连接建立过程。

---

## 5. **进阶技巧**
### **5.1 统计 HTTP 访问最多的主机**
1. 先用 **捕获过滤器**：
   ```plaintext
   tcp port 80
   ```
2. 在 **Wireshark** 菜单栏选择：
   ```plaintext
   Statistics -> HTTP -> Request
   ```
   即可查看 HTTP 访问最多的主机。

---

### **5.2 按 MAC 地址过滤**
```plaintext
eth.src == 00:1A:2B:3C:4D:5E
```
**解释**：仅显示源 MAC 地址为 `00:1A:2B:3C:4D:5E` 的流量。

---

### **5.3 过滤 WebSocket 连接**
```plaintext
tcp.port == 443 && frame contains "Sec-WebSocket-Key"
```
**解释**：筛选 HTTPS WebSocket 握手请求。

---
