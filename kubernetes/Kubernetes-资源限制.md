# Kubernetes 资源限制

## 1. **资源限制**

Kubernetes 的资源限制包括两个主要方面：

- **请求（Requests）**：容器在启动时必须至少可用的资源。它决定了调度器将容器调度到哪个节点。
- **限制（Limits）**：容器在运行过程中可以使用的最大资源。它定义了容器能够使用的资源的上限。

通过合理设置这两个值，Kubernetes 可以确保容器不会占用过多资源，从而保证集群的稳定性和高效性。

---

## 2. **资源请求和限制的设置**

Kubernetes 的资源限制可以在 Pod 或容器的 `spec.containers` 部分中进行设置。每个容器可以独立设置资源请求和限制。

### 2.1 **资源请求（Requests）**

资源请求是容器调度时的基础，表示容器启动时所需要的最低资源量。当调度器选择节点时，会根据节点上可用资源和容器的资源请求来选择适合的节点。资源请求的值是容器被调度到节点上的“保证”资源，容器在运行时将至少获得这些资源。

### 2.2 **资源限制（Limits）**

资源限制是容器运行时所允许的最大资源量。容器在使用超过该限制的资源时，Kubernetes 会采取适当的措施来限制它，如：如果容器的内存超过限制，Kubernetes 会杀死并重启该容器。

---

## 3. **资源请求和限制的配置示例**


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx:latest
    resources:
      requests:
        memory: "64Mi"   # 请求的内存资源
        cpu: "250m"      # 请求的 CPU 资源
      limits:
        memory: "128Mi"  # 限制的内存资源
        cpu: "500m"      # 限制的 CPU 资源
```

在上面的示例中，容器的资源请求和限制配置如下：

- **requests**：
    - `memory: "64Mi"`：容器请求 64 MiB 内存资源。
    - `cpu: "250m"`：容器请求 250 毫核（即 0.25 核）的 CPU 资源。

- **limits**：
    - `memory: "128Mi"`：容器限制 128 MiB 内存资源。
    - `cpu: "500m"`：容器限制 500 毫核（即 0.5 核）的 CPU 资源。

---



## 4. **使用 Resource Quotas 和 LimitRange**

### 4.1 **ResourceQuotas**

`ResourceQuota` 是 Kubernetes 提供的一个资源管理对象，用来限制命名空间中所有对象的资源使用上限。通过设置 `ResourceQuota`，可以对命名空间内的资源总量进行控制，防止某些应用过度消耗资源。

#### 示例：`ResourceQuota`

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: my-quota
spec:
  hard:
    requests.cpu: "4"
    requests.memory: "4Gi"
    limits.cpu: "8"
    limits.memory: "8Gi"
```

此示例中，命名空间的资源限制如下：
- `requests.cpu`：最多可请求 4 个 CPU 核心。
- `limits.cpu`：最多可限制 8 个 CPU 核心。
- `requests.memory`：最多请求 4 GiB 内存。
- `limits.memory`：最多限制 8 GiB 内存。

### 4.2 **LimitRange**

`LimitRange` 是 Kubernetes 中的另一种资源限制对象。它通过定义 Pod 中的容器资源请求和限制的默认值及范围，来约束命名空间内资源的使用。

#### 示例：`LimitRange`

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: my-limit-range
spec:
  limits:
  - max:
      cpu: "2"
      memory: "2Gi"
    min:
      cpu: "100m"
      memory: "128Mi"
    type: Container
```

此示例中，`LimitRange` 限制了每个容器的资源范围：
- 每个容器的最大 CPU 为 2 核，最大内存为 2 GiB。
- 每个容器的最小 CPU 为 100m，最小内存为 128 MiB。

---
