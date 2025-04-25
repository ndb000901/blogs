# minikube 基础使用

## 1. 常用命令

### 1.1 集群生命周期管理

| 命令 | 说明 |
|------|------|
| `minikube start` | 启动 Minikube 集群，可指定驱动、k8s 版本等 |
| `minikube stop` | 停止正在运行的集群（不会删除） |
| `minikube delete` | 删除 Minikube 创建的 VM 或容器 |
| `minikube status` | 查看当前 Minikube 集群状态 |
| `minikube restart` | 重启当前集群 |

#### 示例：

```bash

minikube start --driver=docker --kubernetes-version=v1.29.0
minikube delete --all --purge
```

---

### 1.2 集群信息与配置

| 命令 | 说明 |
|------|------|
| `minikube config set <key> <value>` | 设置默认配置，如默认 driver |
| `minikube ip` | 查看 Minikube 虚拟机 IP |
| `minikube dashboard` | 启动 Kubernetes Web UI（dashboard） |
| `minikube addons list` | 查看可用的插件列表 |
| `minikube addons enable <addon>` | 启用插件（如 ingress, metrics-server） |
| `minikube tunnel` | 创建负载均衡访问通道（如 LoadBalancer 类型 service） |

#### 示例：

```bash

minikube dashboard
minikube addons enable ingress
minikube config set memory 4096
```

---

### 1.3 本地开发辅助命令

| 命令 | 说明 |
|------|------|
| `minikube service <service>` | 打开指定的 service，在浏览器访问 |
| `minikube service list` | 列出所有 Service 的 URL |
| `minikube ssh` | 进入 Minikube 节点（虚拟机 / 容器） |
| `minikube mount <host-dir>:<vm-dir>` | 把主机目录挂载到 Minikube 中 |
| `minikube kubectl` | 直接使用 Minikube 内置的 `kubectl` 命令 |

#### 示例：

```bash

minikube service my-app-service
minikube mount ~/mydata:/data
minikube ssh
```

---

### 1.4 镜像管理

Minikube 默认在一个 VM/容器中运行，有时需要手动构建/加载镜像进去。

| 命令 | 说明 |
|------|------|
| `minikube image build` | 在 Minikube 中构建 Docker 镜像 |
| `minikube image load <image>` | 把本地 Docker 镜像导入到 Minikube |
| `minikube image list` | 查看当前集群中的镜像 |
| `minikube image rm <image>` | 删除镜像 |

#### 示例：

```bash

minikube image load my-app:latest
minikube image build -t myapp:v1 .
```

> 如果使用 `docker` driver，可以直接 `docker build`，镜像自动共享，无需 load。

---

### 1.5 多集群 / 多 profile 支持

Minikube 支持运行多个独立的本地集群。

| 命令 | 说明 |
|------|------|
| `minikube profile list` | 查看当前所有集群 |
| `minikube profile <name>` | 切换当前集群 |
| `minikube start -p <profile>` | 启动一个新集群（不同 profile） |

#### 示例：

```bash
minikube start -p dev-cluster --memory=4096
minikube profile dev-cluster
```

---

## 2. 常用示例

```bash
# 启动带 Ingress 插件的集群
minikube start --addons=ingress --driver=docker

# 查看 dashboard
minikube dashboard

# 启用 metrics-server 自动扩缩容支持
minikube addons enable metrics-server

# 加载一个镜像用于本地测试
minikube image load my-app:latest
```

---


```bash

minikube start \
  -p dev \
  --driver=docker \
  --kubernetes-version=v1.33.0 \
  --cpus=4 \
  --memory=8192 \
  --addons=ingress,dashboard

minikube start \
  -p dev \
  --driver=docker \             # 使用 Docker 启动
  --kubernetes-version=v1.33.0 \# 指定 Kubernetes 版本
  --cpus=4 \                    # 分配 CPU 数量
  --memory=8192 \               # 分配内存
  --addons=ingress,dashboard    # 启用插件
```

---
