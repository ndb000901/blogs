# Nginx-负载均衡

## 1. 基本配置：使用 `upstream`

```nginx
http {
    upstream backend {
        server 192.168.1.101:8080;
        server 192.168.1.102:8080;
    }

    server {
        listen 80;
        server_name yourdomain.com;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

这表示请求 `yourdomain.com` 时，会自动转发到两个后端服务器，默认**轮询调度**。

---

## 2. 常见负载均衡策略

### 2.1 **轮询（默认）**

无需额外配置，自动轮流分发。

```nginx
upstream backend {
    server 192.168.1.101:8080;
    server 192.168.1.102:8080;
}
```

---

### 2.2 **权重（weight）**

可以为每个后端分配权重。

```nginx
upstream backend {
    server 192.168.1.101:8080 weight=3;
    server 192.168.1.102:8080 weight=1;
}
```

表示每 4 个请求中，3 个给 101，1 个给 102。

---

### 2.3 **最少连接数（least\_conn）**

适合长连接或慢接口。

```nginx
upstream backend {
    least_conn;
    server 192.168.1.101:8080;
    server 192.168.1.102:8080;
}
```

---

### 2.4 **IP 哈希（ip\_hash）**

按客户端 IP 分发请求（保持会话粘性）：

```nginx
upstream backend {
    ip_hash;
    server 192.168.1.101:8080;
    server 192.168.1.102:8080;
}
```

---

## 3. 后端服务器选项

* `weight=3`：指定权重
* `max_fails=3 fail_timeout=10s`：失败超过次数后暂时摘除
* `backup`：备用服务器，主服务器全挂才使用

示例：

```nginx
upstream backend {
    server 192.168.1.101:8080 weight=3 max_fails=2 fail_timeout=5s;
    server 192.168.1.102:8080;
    server 192.168.1.103:8080 backup;
}
```

---

