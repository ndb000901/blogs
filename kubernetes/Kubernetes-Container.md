# Kubernetes Container

Kubernetes 中的 Container 是 Pod 的一部分，本质上是使用镜像启动的一个标准 Linux 容器，由底层 CRI 实现（containerd、CRI-O）。

但在 **Kubernetes 上下文中**，一个 Container：

- **必须运行在 Pod 中**
- **与同一 Pod 的其他容器共享网络和卷**
- **由 kubelet 通过 CRI 调用容器运行时启动和管理**

---

## 1. Container 与 Docker 容器的区别

| 特性                     | Docker 容器                            | K8s Container（在 Pod 中）            |
|--------------------------|-----------------------------------------|---------------------------------------|
| 启动方式                 | `docker run`                            | 被 kubelet 拉起                       |
| 网络                     | 独立 IP、端口                          | 共享 Pod 网络空间                     |
| 存储                     | 单独挂载 Volume                        | Pod 层统一挂载 Volume                 |
| 生命周期控制             | 手动或 docker-compose                  | 由 Kubelet、控制器等自动管理          |
| 健康探针                 | 无                                     | 有 Readiness、Liveness Probe          |

---

## 2. 生命周期详解（与 Pod 深度联动）

每个 Container 有自己的生命周期状态：

| 状态         | 含义                                                                 |
|--------------|----------------------------------------------------------------------|
| `Waiting`    | 镜像拉取中、初始化中、或启动失败正在重试                             |
| `Running`    | 正常运行中                                                           |
| `Terminated` | 已退出，带有 exitCode 与原因说明                                     |

控制 Container 生命周期的机制：

- **`restartPolicy`**（Pod 层）决定是否重启该 Container
- **探针（Probe）** 可以决定是否重启容器或从负载均衡中移除
- **`postStart`/`preStop` hook** 用于启动后或关闭前的控制逻辑

---

## 3. 容器配置参数详解

### 启动命令与参数

```yaml
command: ["nginx"]
args: ["-g", "daemon off;"]
```

- `command`: 相当于容器启动进程的 argv[0]
- `args`: 追加参数

> 注意：镜像中定义了 `ENTRYPOINT` 和 `CMD` 的话，这两项会覆盖它。

---

### 挂载卷（volumes）

```yaml
volumeMounts:
  - name: config
    mountPath: /etc/config
```

- 所有挂载目录来自 Pod 中定义的 `volumes`
- 容器间可以共享挂载点（共享数据或配置）

---

### 网络端口声明

```yaml
ports:
  - containerPort: 8080
```

- 虽然容器共享 Pod 的网络，但你可以声明用于 Service 映射的端口

---

### 环境变量注入

```yaml
env:
  - name: ENV_NAME
    value: "prod"
  - name: HOSTNAME
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
```

- 支持直接注入
- 也支持从字段（Pod 名称、Node 名）或 Secret/ConfigMap 动态注入

---

### 健康检查（探针）

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

- `livenessProbe`: 不健康就重启容器
- `readinessProbe`: 不健康就从 Service 负载中移除
- `startupProbe`: 用于容器启动慢时防止过早失败

---

### 资源限制

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

- `requests`: 调度时使用的资源最小值
- `limits`: 超过即触发限制或 OOM Kill

---

### 生命周期 Hook

```yaml
lifecycle:
  postStart:
    exec:
      command: ["sh", "-c", "echo Container started"]
  preStop:
    exec:
      command: ["sh", "-c", "echo Container stopping"]
```

- `postStart`: 容器启动后立即执行
- `preStop`: 优雅退出前执行

---

## 4. 多容器 Pod 场景（常见模式）

| 模式           | 说明                                                               |
|----------------|--------------------------------------------------------------------|
| Sidecar        | 主业务 + 辅助容器，如 Envoy、Log Agent                             |
| Adapter        | 转换主容器输出成标准格式，常用于日志收集                          |
| Ambassador     | 充当代理容器，处理流量路由、链路追踪等                             |

---

## 5. 常用操作命令

### 查看容器状态
```bash

kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[*].state}'
```

### 查看日志
```bash

kubectl logs <pod-name> -c <container-name>
```

### 重启容器（通过修改触发 Rolling Update）
```bash

kubectl rollout restart deployment <deploy-name>
```

---

