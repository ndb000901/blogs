# Kubernetes 核心概念


## 1. 计算与资源管理

| 概念 | 说明 |
|------|------|
| **Node** | 代表一个集群中的实际或虚拟机（一个工作节点） |
| **Pod** | Kubernetes 最小的调度单元，封装了一个或多个容器，**共享网络和存储** |
| **Container** | Pod 中实际运行的应用进程，通常用 Docker、containerd 运行 |
| **Namespace** | 用于资源隔离、权限分区的逻辑空间（比如：`default`, `kube-system`, `dev`） |
| **Resource Request / Limit** | 配置容器的资源需求和上限（CPU、内存），用于调度和限流 |
| **Taint & Toleration** | 节点打“污点”、Pod 设置容忍，实现 Pod 的定向调度 |
| **Node Affinity / Pod Affinity** | 控制 Pod 应该被调度到哪些节点或靠近哪些 Pod |

---

## 2. 应用部署与管理

| 概念 | 说明 |
|------|------|
| **Deployment** | 管理一组 Pod 副本，支持滚动更新、自动恢复等 |
| **ReplicaSet** | 用于确保特定数量的 Pod 副本运行（Deployment 的下层） |
| **StatefulSet** | 类似 Deployment，用于有状态服务，支持稳定网络 ID、存储 |
| **DaemonSet** | 保证每个（或指定）节点上都运行一个 Pod（如日志收集、监控） |
| **Job / CronJob** | 管理一次性任务（Job）或定时任务（CronJob） |
| **HorizontalPodAutoscaler (HPA)** | 基于指标（如 CPU 利用率）自动扩缩容 |
| **Init Containers** | Pod 中先运行的初始化容器，主容器启动前必须完成 |
| **Sidecar 模式** | 常用于服务代理（如 envoy）、日志收集器等附加功能容器 |

---

## 3. 服务与网络通信

| 概念 | 说明 |
|------|------|
| **Service** | 提供对 Pod 的访问抽象，支持负载均衡（ClusterIP / NodePort / LoadBalancer） |
| **ClusterIP** | 默认类型，仅集群内访问 |
| **NodePort** | 每个节点开放一个端口，访问该服务 |
| **LoadBalancer** | 基于云平台自动创建外部负载均衡器 |
| **Headless Service** | 不分配 Cluster IP，用于暴露 Pod 的真实 IP（通常配合 StatefulSet） |
| **Ingress / Ingress Controller** | 通过 HTTP 路由规则暴露服务（如 NGINX Ingress Controller） |
| **NetworkPolicy** | 定义 Pod 之间的访问规则，起到“防火墙”作用 |

---

## 4. 配置与存储

| 概念 | 说明 |
|------|------|
| **ConfigMap** | 注入配置文件、环境变量，支持解耦配置与镜像 |
| **Secret** | 与 ConfigMap 类似，但专用于敏感信息（Base64 编码） |
| **Volume** | Pod 持久化存储抽象，可挂载多种存储（hostPath、NFS、PVC） |
| **PersistentVolume (PV)** | 由管理员预先配置的存储资源 |
| **PersistentVolumeClaim (PVC)** | 用户申请存储资源的请求 |
| **StorageClass** | 定义动态存储的“类型”（如性能/冷存） |

---

## 5. 安全与权限

| 概念 | 说明 |
|------|------|
| **ServiceAccount** | 用于 Pod 访问 Kubernetes API 的身份 |
| **RBAC** | Role-Based Access Control，基于角色的访问控制 |
| **NetworkPolicy** | 控制 Pod 之间及其对外的网络访问 |
| **PodSecurityPolicy / PodSecurity Standards** | 限制 Pod 的权限、用户、挂载等行为（PSP 已弃用） |

---

## 6. 自定义与扩展机制

| 概念 | 说明 |
|------|------|
| **CRD (CustomResourceDefinition)** | 用户定义的自定义资源类型 |
| **Controller / Operator** | 管理资源生命周期的程序（自定义或内置） |
| **Admission Controller / Webhook** | 请求拦截器，用于策略审查和动态变更资源 |

---

