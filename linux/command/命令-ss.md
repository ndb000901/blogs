# ss 命令

`ss`（socket statistics）是 Linux 系统中用于查看网络连接的强大工具，它是 `netstat` 的替代者，速度更快、信息更详细，现代 Linux 系统推荐使用。

---

## 1. 基本格式

```bash

ss [选项]
```

---

## 2. 常用选项详解

### 2.1 `-t`：显示 TCP 连接

```bash

ss -t
```
- `t` = TCP，只列出 TCP 套接字。

---

### 2.2 `-u`：显示 UDP 连接

```bash

ss -u
```
- `u` = UDP，只列出 UDP 套接字。

---

### 2.3 `-l`：仅显示监听的端口

```bash

ss -l
```
- 列出正在监听的 socket，不显示已建立的连接。

---

### 2.4 `-n`：以数字形式显示地址与端口（不进行 DNS 解析）

```bash

ss -n
```
- 避免域名反查，加快速度，端口显示为数字。

---

### 2.5 `-a`：显示所有套接字（监听+已连接）

```bash

ss -a
```

---

### 2.6 `-p`：显示进程信息（需 root 权限）

```bash

ss -p
```
- 显示使用 socket 的程序名称与 PID。

---

### 2.7 `-r`：解析主机名

```bash

ss -r
```
- 显示主机名（默认 `-n` 会禁用它）。

---

### 2.8 `-s`：显示 socket 统计信息

```bash

ss -s
```
- 类似 `netstat -s`，显示 TCP/UDP 等连接总览统计。

---

### 2.9 `-o`：显示计时器信息（如 TCP 重传超时等）

```bash

ss -o
```

---

### 2.10 `-4` / `-6`：仅显示 IPv4 / IPv6 的连接

```bash

ss -4   # IPv4
ss -6   # IPv6
```

---

## 3. 高阶选项与组合用法

### 3.1 查看所有监听端口（含进程）

```bash

ss -tulnp
```

### 3.2 查看 TCP 状态为 ESTABLISHED 的连接

```bash

ss -tna | grep ESTAB
```

### 3.3 查看某个端口的监听情况（如 8080）

```bash

ss -tuln | grep :8080
```

### 3.4 查看某个进程绑定了哪些端口

```bash

ss -lptn | grep nginx
```

### 3.5 持续刷新显示连接（结合 `watch`）

```bash

watch -n 1 ss -s
```

---

## 4. 输出字段说明（示例）

```bash

Netid  State      Recv-Q Send-Q Local Address:Port  Peer Address:Port
tcp    LISTEN     0      128    0.0.0.0:22          0.0.0.0:*
```

- **Netid**：网络类型（如 tcp、udp、unix）
- **State**：连接状态（LISTEN, ESTAB, TIME-WAIT 等）
- **Recv-Q**：接收队列长度
- **Send-Q**：发送队列长度
- **Local Address:Port**：本地地址和端口
- **Peer Address:Port**：对端地址和端口

---

