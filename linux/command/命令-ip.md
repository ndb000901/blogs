# ip 命令

`ip` 是 Linux 系统中用于网络配置和管理的命令，它提供了比传统的 `ifconfig` 命令更多的功能。`ip` 命令可以用于查看、配置和修改网络接口、路由表、网络设备等。

## 1. 基本命令格式

```bash

ip [options] object [command]
```
- `options`：可选参数，用于指定不同的选项。
- `object`：指定要操作的网络对象，如 `link`（网络接口）、`addr`（IP 地址）、`route`（路由表）、`neigh`（邻居表）等。
- `command`：要执行的操作，如 `show`、`add`、`del` 等。

## 2. 常见操作及其用法

### 2.1 **查看网络接口**

```bash

ip link show
```
- 显示系统中所有网络接口的状态信息。

### 2.2 **查看或配置 IP 地址**

```bash

ip addr show
```
- 显示所有网络接口的 IP 地址。

```bash

ip addr show dev eth0
```
- 显示指定网络接口 `eth0` 的 IP 地址。

```bash

ip addr add 192.168.1.10/24 dev eth0
```
- 为 `eth0` 接口添加 IP 地址 `192.168.1.10`，子网掩码为 `/24`。

```bash

ip addr del 192.168.1.10/24 dev eth0
```
- 从 `eth0` 接口删除 IP 地址 `192.168.1.10`。

### 2.3 **查看路由表**

```bash

ip route show
```
- 显示当前的路由表信息。

```bash

ip route add default via 192.168.1.1
```
- 添加默认路由，网关为 `192.168.1.1`。

```bash

ip route del default via 192.168.1.1
```
- 删除指定的默认路由。

```bash

ip route add 192.168.2.0/24 via 192.168.1.1
```
- 添加一条路由，将访问 `192.168.2.0/24` 网络的流量通过网关 `192.168.1.1` 转发。

## 2.4 **管理网络设备**

```bash

ip link set eth0 up
```
- 启用网络接口 `eth0`。

```bash

ip link set eth0 down
```
- 禁用网络接口 `eth0`。

```bash

ip link set eth0 mtu 1500
```
- 设置网络接口 `eth0` 的最大传输单元（MTU）为 `1500`。

### 2.5 **查看邻居表（ARP 表）**

```bash

ip neigh show
```
- 显示邻居（ARP）表，列出与本机直接通信的设备的 MAC 地址与 IP 地址的映射。

```bash

ip neigh add 192.168.1.10 lladdr 00:11:22:33:44:55 dev eth0
```
- 向邻居表中添加一条记录，指定 IP 地址为 `192.168.1.10`，MAC 地址为 `00:11:22:33:44:55`，设备为 `eth0`。

```bash

ip neigh del 192.168.1.10 dev eth0
```
- 删除邻居表中与 `192.168.1.10` 对应的条目。

### 2.6 **管理多播路由**

```bash

ip maddr show
```
- 显示当前网络接口的多播地址。

```bash

ip maddr add 239.255.255.250 dev eth0
```
- 为 `eth0` 接口添加多播地址 `239.255.255.250`。

### 2.7 **查看接口的详细信息**

```bash

ip -s link
```
- 显示网络接口的详细信息，包括接收到和发送的数据包数量、错误、丢包等统计信息。

## 3. 选项说明

- `-s`：显示统计信息，如流量、错误、丢包等。
- `-o`：以简洁的格式输出命令结果。
- `-4`：仅显示 IPv4 地址。
- `-6`：仅显示 IPv6 地址。

## 4. 示例

### 4.1 **查看网络接口信息**

```bash

ip link show
```

输出示例：
```bash

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.1.10/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
```
- 该命令列出所有网络接口及其状态。

### 4.2 **查看路由表**

```bash

ip route show
```
输出示例：
```bash

default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 scope link
```
- 显示默认路由和局域网路由信息。

### 4.3 **查看 ARP 表**

```bash

ip neigh show
```
输出示例：
```bash

192.168.1.1 dev eth0 lladdr 00:11:22:33:44:55 REACHABLE
```
- 显示与 `eth0` 接口关联的 IP 地址与 MAC 地址。

### 4.4 **配置 IP 地址**

```bash

ip addr add 192.168.1.20/24 dev eth0
```
- 为 `eth0` 接口添加 IP 地址 `192.168.1.20`，子网掩码为 `/24`。

### 4.5 **删除 IP 地址**

```bash

ip addr del 192.168.1.20/24 dev eth0
```
- 从 `eth0` 接口删除 IP 地址 `192.168.1.20`。

