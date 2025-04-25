# Docker 网络

Docker 网络是它的重要组成部分，关系到容器间通信、容器与主机或外部网络通信的能力。它建立在 Linux 网络命名空间（network namespace）和虚拟网络设备（如 veth pair）之上，配合 iptables、bridge、macvlan 等机制，实现灵活的网络管理。


## 1. 网络类型（驱动）

### 1.1 **bridge（默认）**
> 本地容器间通信最常用，适用于单主机部署。

- Docker 创建一个 Linux 网桥（如 `docker0`）。
- 每个容器通过 veth pair 接入桥。
- 使用私有 IP（如 172.17.0.0/16）。
- 外部访问需要端口映射（`-p 80:80`）。

```bash

docker run -d --name web -p 8080:80 nginx
```

- 内部通信示意：
  ```
  [容器A: eth0] -- veth pair -- [docker0] -- veth pair -- [容器B: eth0]
  ```

---

### 1.2 **host**
> 容器与主机共享网络栈，**没有网络隔离**。

- 没有独立的网络命名空间。
- 容器使用主机的 IP 和端口。

```bash

docker run --network host nginx
```

适合对网络性能有极致要求的场景，但不推荐用于多容器部署（端口冲突）。

---

### 1.3 **none**
> 容器网络完全禁用，适合手动管理。

```bash

docker run --network none busybox
```

容器没有网络接口，除 loopback。

---

### 1.4 **overlay**
> 用于跨主机容器通信，需要 Swarm/Kubernetes 支持。

- 构建于 vxlan 隧道技术之上。
- 在 Swarm 集群中创建：
  ```bash
  docker network create --driver overlay my_overlay
  ```

---

### 1.5 **macvlan**
> 容器以主机局域网中一个“伪物理机”身份出现（拥有独立 MAC、IP）。

- 容器可以像物理主机一样被局域网识别。
- 适用于需要访问物理网络（如 DHCP）的场景。

```bash

docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 my_macvlan
```

---


## 2. `docker network` 命令


```bash

docker network [COMMAND]
```

### 2.1 常用子命令列表：

| 子命令            | 说明 |
|-------------------|------|
| `ls`              | 列出所有网络 |
| `inspect`         | 查看某个网络的详细信息 |
| `create`          | 创建自定义网络 |
| `rm`              | 删除一个或多个网络 |
| `connect`         | 将容器连接到指定网络 |
| `disconnect`      | 将容器从指定网络断开 |

---

### 2.2 `docker network ls`
列出当前 Docker 引擎上的所有网络：

```bash

docker network ls
```

#### 输出示例：

```text
NETWORK ID     NAME      DRIVER    SCOPE
2e3b7cdbf4d9   bridge    bridge    local
19d87a3f2dd4   host      host      local
f13db46b2c45   none      null      local
```

- **NAME**：网络名称
- **DRIVER**：网络类型，如 `bridge`、`overlay`、`host`
- **SCOPE**：`local`（单机）或 `swarm`（集群）

---

### 2.3 `docker network inspect <network>`
查看网络详细配置（包括 IP 段、连接的容器等）：

```bash

docker network inspect bridge
```

输出是一个 JSON，包含以下信息：

- `Subnet` / `Gateway`
- 已连接的容器及 IP
- 网络驱动和选项

---

### 2.4 `docker network create`
创建一个自定义网络：

```bash

docker network create mynet
```

### 常用参数：

| 参数 | 说明 |
|------|------|
| `--driver`         | 指定驱动（默认为 bridge） |
| `--subnet`         | 指定子网 |
| `--gateway`        | 指定网关 |
| `--ip-range`       | 指定 IP 池范围 |
| `--attachable`     | 允许非 Swarm 容器附加（overlay 网络中用） |
| `--internal`       | 该网络不能访问外部网络 |

#### 示例：

```bash

docker network create \
  --driver bridge \
  --subnet 192.168.100.0/24 \
  --gateway 192.168.100.1 \
  my_custom_net
```

---

### 2.5 `docker network rm <network>`
删除网络（系统默认的 `bridge`、`host`、`none` 不能删）：

```bash

docker network rm mynet
```

网络必须为空（无容器连接）才能删除。

---

### 2.6 `docker network connect`
把某个容器添加到一个已有网络：

```bash

docker network connect mynet mycontainer
```

可以实现**一个容器连接多个网络**的场景（如暴露接口在两个不同网段中）。

---

### 2.7 `docker network disconnect`
将容器从某个网络中移除：

```bash

docker network disconnect mynet mycontainer
```

如果这个网络是容器启动时指定的默认网络，移除后容器会失联。

---





