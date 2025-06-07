# Nginx-UDP代理

## 1. 检查是否启用 `--with-stream`

需要 **在编译 Nginx 时加上 `--with-stream` 参数** 才能支持 UDP 代理：

```bash

./configure --prefix=/opt/nginx --with-stream
make -j$(nproc)
make install
```

验证：

```bash

/opt/nginx/sbin/nginx -V
```

输出中应包含：

```
--with-stream
```

---

## 2. 配置 UDP 代理（`stream` 模块）

Nginx 的 `stream` 块用于 TCP/UDP 代理。

```nginx
stream {
    upstream game_servers {
        server 10.0.0.1:9000;
        server 10.0.0.2:9000;
        server 10.0.0.3:9000;
    }

    server {
        listen 9000 udp;
        proxy_pass game_servers;
    }
}
```

注意：UDP 不是连接型协议，**没有粘性连接的概念**，所有流量会根据请求轮询调度。

---

## 3. 日志配置

在 `nginx.conf` 中启用 stream 日志：

```nginx
stream {
    log_format proxy_log '$remote_addr [$time_local] '
                         '$protocol $status $bytes_sent $bytes_received '
                         '$session_time "$upstream_addr"';

    access_log /opt/nginx/logs/udp_access.log proxy_log;

    upstream game_servers {
        server 10.0.0.1:9000;
        server 10.0.0.2:9000;
        server 10.0.0.3:9000;
    }

    server {
        listen 9000 udp;
        proxy_pass game_servers;
    }
}
```

---

