# Kubernetes Pod


**Pod 是 Kubernetes 中最小的可调度部署单元。**

它封装了：
- 一个或多个**紧密关联的容器**
- 共享的 **网络（IP、端口）**
- 共享的 **存储卷**
- 容器间的**进程通信**

> 一个 Pod 里的多个容器通常是“主-辅”模式，例如主容器和 sidecar。

---

## 1. Pod 的结构图：

```text
+-----------------------------------------------------+
|                     Pod（一个 IP）                   |
| +-----------------+   +-------------------------+   |
| |   容器 A         |   |    容器 B（sidecar）     |   |
| |  nginx、app等    |   |    logagent、envoy等    |   |
| +-----------------+   +-------------------------+   |
|                                                     |
|     共享的 Network Namespace（一个IP、一套端口）        |
|     共享的 Volumes（挂载目录）                         |
+-----------------------------------------------------+
```

---

## 2. Pod 的工作原理

- **Pod IP 是唯一的**，但只在所在 Node 内可路由，多个容器共享。
- **容器之间通过 `localhost` 通信**（共享网络命名空间）。
- **Pod 中容器生命周期由 kubelet 管理**，Pod 生命周期由 kube-apiserver 决定。

---

## 3. Pod 的生命周期阶段（`kubectl describe pod` 可见）

| 阶段         | 含义                                                                 |
|--------------|----------------------------------------------------------------------|
| `Pending`    | Pod 已提交但尚未被调度或容器镜像尚未下载完成                        |
| `Running`    | Pod 已在 Node 上运行                                                  |
| `Succeeded`  | 所有容器都已成功退出（适用于一次性 Job）                              |
| `Failed`     | 某个容器非 0 状态退出，且未设置重启策略                               |
| `Unknown`    | kubelet 无法访问此 Pod                                               |

---

## 4. Pod 的重启策略

通过 `restartPolicy` 控制（默认 Always）：

- `Always`：无论退出原因，都重启（用于 Deployment）
- `OnFailure`：非 0 状态退出时重启（适用于 Job）
- `Never`：不重启（Job/调试场景）

---

## 5. Pod 的类型（按控制器分类）

| 类型                  | 说明                             |
|-----------------------|----------------------------------|
| 普通 Pod              | 手动创建，不建议用于生产         |
| ReplicaSet Pod        | 有副本控制器管理                 |
| Deployment Pod        | 有版本控制、滚动更新能力         |
| StatefulSet Pod       | 有固定 Pod 名 + 有序启动         |
| DaemonSet Pod         | 每个 Node 启一个（日志、监控）   |
| Job/CronJob Pod       | 执行完就结束                     |

---

## 6. Pod 的网络模型（CNI）

- 每个 Pod 都有一个 IP（由 CNI 插件分配）
- Pod 到 Pod 通信 **无需 NAT**
- kube-proxy 实现 Service 层的负载均衡

---

## 7. Pod 的挂载卷（Volumes）

Pod 是数据挂载的起点：

- `emptyDir`: Pod 生命周期内共享目录
- `hostPath`: 挂载宿主机目录
- `configMap`/`secret`: 注入配置
- `persistentVolumeClaim`: 外部持久卷（如 NFS）

---


## 8. 常用命令

### 查看 Pod 列表

```bash

kubectl get pods
```

### 查看 Pod 详情

```bash

kubectl describe pod <pod-name>
```

### 进入 Pod 容器

```bash

kubectl exec -it <pod-name> -- /bin/sh
```

### 查看 Pod 所在 Node

```bash

kubectl get pod -o wide
```

---
