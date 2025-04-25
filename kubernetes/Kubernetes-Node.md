# Kubernetes Node

`Node` 是 Kubernetes 中的工作机器，负责运行 **Pod（容器）**。每个 Node 是集群中的一台虚拟机或物理机，**由 Master 节点控制调度与管理**。

一个典型的 Node 上会运行：

| 组件              | 作用                                                                 |
|-------------------|----------------------------------------------------------------------|
| **kubelet**       | 管理本机的 Pod 生命周期，和 apiserver 通信                           |
| **kube-proxy**    | 负责 Node 网络代理，维护 iptables 规则或 IPVS 转发规则              |
| **Container Runtime** | 例如 Docker、containerd、CRI-O，用来实际运行容器                  |

---

## 1. Node 的核心作用

- **资源提供者**：CPU、内存、存储等供调度器使用。
- **Pod 运行环境**：运行所有容器（Pod）。
- **调度目标单位**：K8s Scheduler 会根据资源和约束选择合适的 Node。
- **健康报告者**：定期汇报状态给 Master（apiserver）。
- **网络通信者**：Pod 间服务通信、跨节点通信由其管理（kube-proxy）。

---

## 2. Node 的结构图：

```text
+------------------------------------------------------------+
|                         Kubernetes Node                    |
|                                                            |
|  +------------------------+   +-------------------------+  |
|  |   kubelet              |   |    kube-proxy           |  |
|  |  (Pod管理/状态上报)      |   |  (Service负载均衡代理) |  |
|  +------------------------+   +-------------------------+  |
|  +------------------------------------------------------+  |
|  |         Container Runtime (Docker/containerd)        |  |
|  +------------------------------------------------------+  |
|      |            |             |             |            |
|  +--------+   +--------+   +--------+   +--------+         |
|  |  Pod1  |   |  Pod2  |   |  Pod3  |   |  Pod4  |  ...... |
|  +--------+   +--------+   +--------+   +--------+         |
|                                                            |
+------------------------------------------------------------+
```

---

## 3. Node 的状态（通过 `kubectl get nodes` 查看）

每个 Node 都有以下状态属性：

### 3.1 `STATUS` 字段

| 状态     | 含义                              |
|----------|-----------------------------------|
| `Ready`  | 正常运行，可以调度 Pod           |
| `NotReady` | kubelet 未就绪/网络断联等        |
| `Unknown`  | 通信失败，apiserver收不到心跳     |

### 3.2 `CONDITIONS`


在 Kubernetes 中，Node Conditions 用于指示 Node 的健康状态和资源压力情况。可以通过 `kubectl describe node <node-name>` 命令查看节点的 Conditions 信息。Conditions 是一个数组，每个 Condition 包含以下字段：

* **Type**: Condition 的类型，例如 `Ready`, `DiskPressure`, `MemoryPressure`, `PIDPressure`, `NetworkUnavailable`。
* **Status**: Condition 的状态，可以是 `True`, `False`, 或 `Unknown`。
* **Reason**: Condition 发生变化的原因的简短、机器可读的标识符。
* **Message**: 关于 Condition 状态的更详细的人工可读的消息。
* **LastTransitionTime**: Condition 最后一次发生变化的时间。

以下是常见的 Node Conditions 及其详细解释：

| Condition Type        | Description                                                                                                                               | Status `True`                                      | Status `False`                                                                 | Status `Unknown`                                                                      |
| :-------------------- | :---------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------- | :----------------------------------------------------------------------------- | :------------------------------------------------------------------------------------ |
| **Ready** | 指示节点是否健康并且准备好接受 Pod。                                                                                                          | 节点是健康的，kubelet 也在正常运行。                      | 节点不健康，kubelet 可能存在问题，无法运行新的 Pod。                               | 节点控制器在最近一段时间（默认 40 秒）没有收到节点的心跳信息。                         |
| **DiskPressure** | 指示节点上的磁盘空间是否不足。                                                                                                                | 节点磁盘空间充足。                                   | 节点磁盘空间不足，可能会影响 Pod 的运行和新 Pod 的调度。                           | 节点控制器无法确定节点的磁盘压力状态。                                                  |
| **MemoryPressure** | 指示节点上的内存是否不足。                                                                                                                  | 节点内存充足。                                     | 节点内存不足，可能会导致 Pod 被驱逐 (evicted) 或影响节点稳定性。                   | 节点控制器无法确定节点的内存压力状态。                                                  |
| **PIDPressure** | 指示节点上运行的进程数量是否过多。                                                                                                            | 节点上运行的进程数量在正常范围内。                       | 节点上运行的进程数量过多，可能会导致系统不稳定。                                 | 节点控制器无法确定节点的 PID 压力状态。                                                 |
| **NetworkUnavailable** | 指示节点的网络是否没有正确配置。                                                                                                              | 节点的网络已正确配置，Pod 可以正常进行网络通信。        | 节点的网络没有正确配置，Pod 可能无法进行网络通信。                               | 节点控制器无法确定节点的网络可用性。                                                    |



## 4. Node 的生命周期（简要流程）

1. Node 注册到集群（手动或自动）
2. kubelet 发送心跳到 apiserver（每 10 秒）
3. controller-manager 检查 Node 是否健康
4. 如果 Node 宕机，Pod 会被标记为失联，最终可能被驱逐

---

## 5. Node 的注册方式

- **静态注册（手动添加）**  
  创建 Node 资源对象（用于虚拟节点、边缘场景）

- **动态注册（默认）**  
  kubelet 启动时加上 `--register-node=true` 会自动注册自身。

---

## 6. 相关命令

### 查看节点列表

```bash

kubectl get nodes
```

### 查看单个节点详情

```bash

kubectl describe node <node-name>
```

### 打标签

```bash

kubectl label node <node-name> zone=shenzhen
```

### 给节点打污点（阻止调度）

```bash

kubectl taint nodes <node-name> key=value:NoSchedule
```

---


