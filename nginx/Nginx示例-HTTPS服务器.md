# Nginx示例-HTTPS服务器


## 1. `conf.d/apis.dev.hello.work.conf`配置文件

```conf
server {

    listen 443 ssl;
    server_name apis.dev.hello.work;

    ssl_certificate     certs/server-apis.dev.hello.work.fullchain.pem; # 证书链
    ssl_certificate_key certs/apis.dev.hello.work.key.pem; # 服务器私钥

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://192.168.43.242:10000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }
}
```

## 2. nginx.conf 配置文件

将`/etc/nginx/conf.d/`下所有域名配置包含进http块

```conf
http {
    include /etc/nginx/conf.d/*.conf;
}
```

## 3. 重载配置

```bash

nginx -t
nginx -s reload
```