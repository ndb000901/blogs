# Kubernetes Ingress

## 1. **Ingress 的作用**

- **HTTP 路由**：Ingress 可以根据请求的 URL 路径或主机名将流量路由到不同的服务。
- **负载均衡**：Ingress 提供基于 HTTP(S) 的负载均衡。
- **SSL 终止**：Ingress 可以配置 SSL 证书来加密客户端与集群之间的通信。
- **域名路由**：Ingress 允许通过域名路由流量到不同的服务。

与 **Service** 不同，Ingress 不直接暴露服务的端口，而是通过 HTTP 路由规则来控制外部流量的转发。

## 2. **Ingress 控制器**

Ingress 是一个 API 对象，而不是直接提供流量管理的组件。实际的流量控制和路由功能是由 **Ingress 控制器** 来实现的。Ingress 控制器是一个部署在集群中的应用，它监视集群中的 Ingress 资源，并根据定义的规则处理传入的流量。

常见的 Ingress 控制器包括：
- **NGINX Ingress Controller**：最常用的 Ingress 控制器，基于 NGINX 提供负载均衡和路由功能。
- **Traefik**：一个现代的反向代理和负载均衡器，也常用作 Ingress 控制器。
- **HAProxy Ingress Controller**：使用 HAProxy 作为负载均衡器的 Ingress 控制器。

选择 Ingress 控制器时，需要根据集群的需求来决定，NGINX 是最常用的选择。

## 3. **Ingress 的基本组成**

Ingress 资源由以下几个部分组成：

### 3.1 **apiVersion, kind, metadata**
- **apiVersion**：指定使用的 Kubernetes API 版本（通常是 `networking.k8s.io/v1`）。
- **kind**：固定为 `Ingress`。
- **metadata**：Ingress 对象的元数据，通常包括 `name` 和 `namespace` 等。

### 3.2 **spec**
`spec` 是 Ingress 的核心部分，定义了流量的路由规则。它包含了多个字段：

- **rules**：定义路由规则，通常是基于 `host` 和 `path` 进行匹配。
    - **host**：指定域名，当请求的 Host 与该字段匹配时，Ingress 会将请求路由到对应的服务。
    - **http**：HTTP 路由规则，包含 `paths` 和每个路径对应的服务。
        - **path**：指定 URL 路径，当请求的路径匹配时，Ingress 会将流量转发到对应的服务。
        - **pathType**：指定路径匹配的方式，常用的值是 `Prefix`（前缀匹配）和 `Exact`（精确匹配）。
        - **backend**：指定流量应该转发到哪个服务，包含服务名称和端口。

- **tls**：可选字段，用于配置 TLS 终止，即为域名配置 SSL 证书，安全地加密传输。需要指定证书和域名。

### 3.3 **Ingress 示例**

以下是一个基本的 Ingress 配置示例：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  namespace: default
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: myapp-tls-secret
```

这个示例中的 Ingress 配置做了以下事情：
- 当访问 `myapp.example.com/app1` 时，流量会被路由到 `app1-service` 服务。
- 当访问 `myapp.example.com/app2` 时，流量会被路由到 `app2-service` 服务。
- 配置了 TLS 终止，`myapp.example.com` 会使用 `myapp-tls-secret` 中的证书。

## 4. **Ingress Controller 的安装**

Ingress 控制器必须在集群中安装和配置，以便支持 Ingress 资源的路由功能。以下是安装 NGINX Ingress 控制器的示例：

1. **安装 NGINX Ingress 控制器**：

   使用 Helm 安装 NGINX Ingress 控制器：
   ```bash
   helm install nginx-ingress stable/nginx-ingress
   ```

   或者使用 `kubectl` 部署官方提供的 YAML 文件：
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
   ```

2. **验证 Ingress 控制器**：

   检查 NGINX Ingress 控制器是否已经部署：
   ```bash
   kubectl get pods -n ingress-nginx
   ```

   输出类似于：
   ```
   NAME                                        READY   STATUS    RESTARTS   AGE
   nginx-ingress-controller-5d8c5b5c9f-xzj4v   1/1     Running   0          2m
   ```

3. **检查 Ingress 控制器的 Service**：

   默认情况下，NGINX Ingress 控制器会创建一个 Service（通常是 `LoadBalancer` 类型），可以通过该服务暴露外部访问：
   ```bash
   kubectl get svc -n ingress-nginx
   ```

   输出类似于：
   ```
   NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
   nginx-ingress-controller             LoadBalancer   10.0.0.100     <external-ip>   80:30516/TCP,443:30273/TCP   5m
   ```

   这里的 `EXTERNAL-IP` 是暴露给外部的 IP 地址，用户可以通过这个 IP 访问 Ingress 控制器。
