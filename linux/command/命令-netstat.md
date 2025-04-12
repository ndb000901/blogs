# netstat 命令

`netstat`（network statistics）是 Linux 系统中用于显示网络连接、路由表、接口状态、网络协议统计信息的命令。虽然在较新的系统中被 `ss` 替代，但在许多系统上仍然常用。

---

## 1. 基本格式

```bash

netstat [选项]
```

---

## 2. 常用选项详解

### 2.1 `-a`：显示所有连接（监听+非监听）

```bash

netstat -a
```
- 显示系统当前所有的网络连接，包括 TCP 和 UDP 的监听与非监听状态。

---

### 2.2 `-t`：仅显示 TCP 连接

```bash

netstat -at
```
- `t` = TCP，列出所有 TCP 协议的连接。

---

### 2.3 `-u`：仅显示 UDP 连接

```bash

netstat -au
```
- `u` = UDP，列出所有 UDP 协议的连接。

---

### 2.4 `-l`：仅显示监听中的服务端口

```bash

netstat -l
```
- 列出所有正在监听（Listen）状态的端口（不包括已连接的）。

---

### 2.5 `-n`：以数字形式显示地址和端口（不进行域名解析）

```bash

netstat -n
```
- 显示 IP 地址而不是主机名，端口号而不是服务名，加快输出速度。

---

### 2.6 `-p`：显示使用该连接的进程 PID 和程序名

```bash

netstat -tulnp
```
- 结合其他参数使用，能看到哪个程序在使用哪些端口。

---

### 2.7 `-r`：显示路由表信息

```bash

netstat -r
```
- 类似 `route -n`，显示内核路由表。

---

### 2.8 `-i`：显示网络接口状态

```bash

netstat -i
```
- 显示每个网络接口的状态（如 `eth0`、`lo` 等）。

---

### 2.9 `-s`：显示各协议统计信息

```bash

netstat -s
```
- 显示 IP、TCP、UDP、ICMP 等协议的流量统计。

---

### 2.10 `-c`：持续输出

```bash

netstat -c
```
- 每隔一秒刷新一次连接信息（类似 `top` 行为）。

---

## 3. 实用命令组合

### 3.1 查看所有监听端口及其进程名

```bash

netstat -tulnp
```

### 3.2 查看指定端口是否被占用

```bash

netstat -tuln | grep 8080
```

### 3.3 查看所有 TCP 状态为 ESTABLISHED 的连接

```bash

netstat -tn | grep ESTABLISHED
```

### 3.4 查看所有监听的本地地址和端口

```bash

netstat -ln
```

---

## 4. 常见输出字段解释（`netstat -tuln`）

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
```

- **Proto**：协议类型（TCP/UDP）
- **Recv-Q**：接收队列中数据的字节数
- **Send-Q**：发送队列中数据的字节数
- **Local Address**：本地地址和端口
- **Foreign Address**：远程地址和端口
- **State**：连接状态（如 LISTEN、ESTABLISHED）

---

## 5. 补充说明

- `netstat` 属于 `net-tools` 包，现代系统可能默认未安装，可用如下命令安装：

```bash

# Debian/Ubuntu
sudo apt install net-tools

# CentOS/RHEL
sudo yum install net-tools
```


 
