# Nginx示例-限流

## 1. 请求速率限流（`limit_req`）

### 配置示例：限制每个 IP 每秒 1 个请求，突发允许 5 个

```nginx
http {
    limit_req_zone $binary_remote_addr zone=req_limit_per_ip:10m rate=1r/s;

    server {
        listen 80;
        server_name example.com;

        location /api/ {
            limit_req zone=req_limit_per_ip burst=5 nodelay;

            proxy_pass http://backend;
        }
    }
}
```

### 参数说明：

* `limit_req_zone`：定义限流区域（内存区域名 + 限速规则）

  * `$binary_remote_addr`：以客户端 IP 做 key
  * `10m`：共享内存大小，1MB 可存约 16,000 个 IP
  * `rate=1r/s`：每秒最多 1 个请求
* `burst=5`：允许瞬时突发最多 5 个请求
* `nodelay`：超出速率但不延迟（立即拒绝）

### 限流超限返回：默认是 `503`

可使用 `error_page` 自定义响应内容。

---

## 2. 连接数限流（`limit_conn`）

### 示例：限制每个 IP 同时最多 2 个连接

```nginx
http {
    limit_conn_zone $binary_remote_addr zone=conn_limit_per_ip:10m;

    server {
        listen 80;
        server_name example.com;

        location / {
            limit_conn conn_limit_per_ip 2;

            proxy_pass http://backend;
        }
    }
}
```

### 参数说明：

* `limit_conn_zone`：定义限流 key 和内存区
* `limit_conn`：设置每个 key 允许的最大并发连接数

---

## 3. 两者联合使用

```nginx
http {
    limit_req_zone $binary_remote_addr zone=req_limit_per_ip:10m rate=2r/s;
    limit_conn_zone $binary_remote_addr zone=conn_limit_per_ip:10m;

    server {
        listen 80;
        server_name api.example.com;

        location /api/ {
            limit_req zone=req_limit_per_ip burst=5 nodelay;
            limit_conn conn_limit_per_ip 1;

            proxy_pass http://backend;
        }
    }
}
```

---

## 4. 自定义限流返回状态和内容

```nginx
server {
    error_page 503 /custom_503.html;

    location = /custom_503.html {
        return 200 "Request rate too high, try later.";
        add_header Content-Type text/plain;
    }
}
```

---




