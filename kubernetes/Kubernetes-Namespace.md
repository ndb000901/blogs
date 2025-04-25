# Kubernetes Namespace


Namespace 是 Kubernetes 的一种资源分区方式，允许将集群中的资源进行逻辑上的划分。每个 Kubernetes 集群都有一个默认的 `default` Namespace，除此之外，可以自定义多个 Namespace 来分隔不同的工作负载和资源。

**重要概念：**
- **Pod、Service、ConfigMap、Secret 等资源** 都可以绑定到一个 Namespace 中。
- 一个 Namespace 内部的资源可以通过名称访问，然而不同 Namespace 之间的资源是相互隔离的，除非显式地配置跨 Namespace 的访问。

---

## 1. 用途

### **资源隔离**
Namespace 提供了一种对集群资源的逻辑隔离，使得不同团队或不同环境（开发、测试、生产等）能够共享一个 Kubernetes 集群，但又相互隔离。例如，不同的开发人员或团队可以在不同的 Namespace 中运行各自的应用，避免互相干扰。

### **资源配额管理**
可以为每个 Namespace 配置资源配额（Resource Quotas），限制其使用的 CPU、内存、存储等资源，避免某些 Namespace 占用集群中过多资源。

### **访问控制**
Namespace 配合 **RBAC**（基于角色的访问控制）可以限制某些用户或服务账户只访问某些 Namespace，从而增强集群的安全性。

### **环境隔离**
在同一个集群中，你可以使用不同的 Namespace 来区分开发环境、测试环境和生产环境。通过不同的 Namespace，你可以保证不同环境间的资源不发生冲突。

---

## 2. Namespace 的创建与管理

### 创建 Namespace

可以通过 `kubectl` 命令创建新的 Namespace。

```bash

kubectl create namespace <namespace-name>
```

例如，创建一个名为 `dev` 的 Namespace：

```bash

kubectl create namespace dev
```

也可以通过 YAML 文件来定义 Namespace。

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

然后使用 `kubectl` 应用该 YAML 文件：

```bash

kubectl apply -f namespace.yaml
```

### 查看 Namespace

查看当前集群中的所有 Namespace：

```bash

kubectl get namespaces
```

或者简写为：

```bash

kubectl get ns
```

### 删除 Namespace

如果不再需要某个 Namespace，可以通过以下命令删除它：

```bash

kubectl delete namespace <namespace-name>
```

### 默认 Namespace

Kubernetes 集群在没有指定 Namespace 的情况下，默认会使用 `default` Namespace。可以通过命令显式指定所使用的 Namespace，或者在 kubectl 配置文件中进行设置。

---

## 3. **在 Namespace 中运行资源**

### 为 Pod、Service 等指定 Namespace

Kubernetes 中的资源可以通过指定 `namespace` 来分配给不同的 Namespace。例如，当创建 Pod 时，可以在 YAML 文件中指定：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: dev
spec:
  containers:
  - name: nginx
    image: nginx
```

通过此配置，Pod 将会被创建在 `dev` Namespace 中。

### 切换当前 Namespace

如果经常使用某个 Namespace，可以通过 `kubectl config set-context` 命令切换当前的默认 Namespace。

```bash

kubectl config set-context --current --namespace=dev
```

这样，之后的所有 kubectl 命令都会默认使用 `dev` Namespace，直到你切换为其他 Namespace。

### 指定命名空间操作

也可以直接在 `kubectl` 命令中指定 Namespace：

```bash

kubectl get pods -n dev
```

---

## 4. **Namespace 的高级使用**

### 4.1 **资源配额（Resource Quotas）**
可以通过为 Namespace 设置资源配额，来限制该 Namespace 可使用的 CPU、内存和存储等资源。

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: resource-quota
  namespace: dev
spec:
  hard:
    cpu: "4"
    memory: "8Gi"
    pods: "10"
```

上述示例会限制 `dev` Namespace 最多使用 4 个 CPU、8Gi 内存和 10 个 Pod。

### 4.2 **Network Policies**
通过 Network Policies，可以限制不同 Namespace 或同一 Namespace 内的 Pod 之间的网络访问。例如，如果希望 `dev` Namespace 中的 Pod 无法访问 `prod` Namespace 中的服务，可以设置如下 NetworkPolicy：

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-prod
  namespace: dev
spec:
  podSelector: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: prod
```

### 4.3 **跨 Namespace 资源访问**
默认情况下，Kubernetes 中不同 Namespace 之间的资源是隔离的。例如，一个 `dev` Namespace 中的 Pod 无法访问 `prod` Namespace 中的服务。但可以通过 Service 的 `namespace` 字段显式地跨 Namespace 进行访问：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: prod-service
  namespace: prod
spec:
  ports:
    - port: 80
  selector:
    app: prod-app
```

在 `dev` Namespace 中访问 `prod-service`：

```bash

curl prod-service.prod.svc.cluster.local
```

---


