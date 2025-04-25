# Kubernetes Service

## 1. **Service 的作用**

Kubernetes 中的 Pod 是动态的，意味着 Pod 可能会被频繁地创建和销毁，这使得直接通过 Pod IP 进行访问变得不可靠。**Service** 通过为 Pod 提供一个稳定的访问入口，解决了这一问题。它通过以下方式提供稳定性：

- **负载均衡**：Service 会自动将流量分发到它所关联的 Pod。
- **服务发现**：Service 为一组 Pod 提供一个虚拟 IP 地址，使得其他应用可以通过固定的地址访问这些 Pod，而无需关心具体的 Pod IP。

## 2. **Service 的类型**

### 2.1 **ClusterIP（默认类型）**

`ClusterIP` 是 Kubernetes 中的默认服务类型，它为服务分配一个集群内的虚拟 IP 地址。通过这个 IP 地址，集群内的其他 Pod 可以访问这个服务。

- **用途**：适用于集群内部的通信。
- **示例**：
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 8080
    type: ClusterIP
  ```

  在这个例子中，`my-service` 会为 `my-app` 选择器匹配的所有 Pod 提供负载均衡，其他 Pod 可以通过 `my-service` 的虚拟 IP 或 DNS 名称进行访问。

### 2.2 **NodePort**

`NodePort` 通过在每个节点上开放一个端口，使得集群外部可以通过 `<NodeIP>:<NodePort>` 的方式访问服务。它通常用来暴露服务，使得集群外部的用户可以访问 Kubernetes 中的应用。

- **用途**：适用于暴露集群服务到外部。
- **示例**：
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 8080
        nodePort: 30007
    type: NodePort
  ```

  通过 `NodePort`，每个集群节点的 30007 端口将映射到服务的 80 端口，外部用户可以通过 `<NodeIP>:30007` 访问到服务。

### 2.3 **LoadBalancer**
`LoadBalancer` 类型的 Service 会自动为服务配置一个外部负载均衡器。大多数云平台（如 AWS、Azure、GCP）支持该类型，它会为服务分配一个公网 IP，使得外部可以直接访问服务。

- **用途**：适用于在云环境中暴露应用，提供外部访问。
- **示例**：
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 8080
    type: LoadBalancer
  ```

  在该配置中，Kubernetes 会自动创建并配置一个外部负载均衡器来为服务提供访问。

### 2.4 **ExternalName**
`ExternalName` 类型的 Service 用于将 Service 映射到外部的服务上。它通过 DNS 查找，将请求转发到指定的外部服务，而不需要将请求引导到集群内的 Pod。

- **用途**：适用于将 Kubernetes 集群内的服务映射到外部服务（如数据库、第三方 API 等）。
- **示例**：
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    type: ExternalName
    externalName: example.com
  ```

  在这个例子中，`my-service` 会将所有流量转发到外部的 `example.com` 上。

---

## 3. **Service 的基本组成**

一个 Service 对象包含几个关键的字段：

- **metadata.name**：Service 的名称。
- **spec.selector**：选择器，用于选择哪些 Pod 被包含在该 Service 中。通常是通过标签选择器来匹配一组 Pod。
- **spec.ports**：定义了服务暴露的端口和目标端口，决定了请求如何路由到 Pod。
    - **port**：Service 的端口。
    - **targetPort**：Pod 中容器的端口。
    - **nodePort**（如果是 `NodePort` 类型）: 公开的端口，可以通过集群节点的 IP 地址访问。
- **spec.type**：指定 Service 的类型，可以是 `ClusterIP`、`NodePort`、`LoadBalancer` 或 `ExternalName`。

---

## 4. **Service 发现**

在 Kubernetes 中，Service 提供了一种自动化的服务发现机制，使得应用可以通过 DNS 或 IP 地址轻松地发现和连接到其他服务。

- **DNS 服务发现**：Kubernetes 内部提供了基于 DNS 的服务发现。每当创建一个 Service 时，Kubernetes 会为它分配一个 DNS 名称（`<service-name>.<namespace>.svc.cluster.local`）。容器可以使用这个 DNS 名称访问 Service。例如，如果你创建了名为 `my-service` 的 Service，其他 Pod 可以通过 `my-service.default.svc.cluster.local` 来访问它。

- **环境变量服务发现**：Kubernetes 还会为每个 Pod 创建与 Service 对应的环境变量。虽然这种方式已经过时，但仍然可用。每当创建一个 Service 时，Kubernetes 会将与该 Service 相关的环境变量注入到 Pod 的环境中。

---

## 5. **Service 与 Pod 的关系**

- Service 是一个抽象层，实际上并不运行应用程序，它只是提供一个稳定的访问接口。
- Service 会通过 **Selector** 将流量转发到符合条件的 Pod，Pod 通过标签进行标识。
- Pod 的生命周期是动态的，而 Service 提供了一个稳定的接入点，帮助解决 Pod 动态变化的问题。

---

## 6. **Service 的负载均衡**

Service 会根据其选择的 Pod 来自动进行负载均衡，支持以下负载均衡策略：

- **轮询（Round Robin）**：默认的负载均衡策略，流量会均匀分配到所有可用的 Pod。
- **Session Affinity（会话亲和性）**：允许将来自同一客户端的请求路由到同一 Pod 上，适用于需要会话保持的应用（例如购物车、用户状态）。

通过设置 `sessionAffinity` 字段，Kubernetes 可以启用会话亲和性：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  sessionAffinity: ClientIP
```

这样设置后，来自相同客户端 IP 的请求会被路由到相同的 Pod。

---

