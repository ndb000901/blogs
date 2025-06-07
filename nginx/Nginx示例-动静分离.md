# Nginx示例-动静分离


在 Nginx 中配置 **动静分离**，是指让：

* **动态请求（如 PHP、Java、Python 接口）** 转发到后端应用服务器；
* **静态资源（如 HTML、JS、CSS、图片等）** 直接由 Nginx 处理，不经过后端。

这种做法可以极大提升性能和吞吐能力。

---

## 1. 动静分离的基本原理

* Nginx 使用 `location` 匹配请求路径。
* 静态文件：匹配 `.css` `.js` `.png` 等后缀，`root` 或 `alias` 返回文件。
* 动态接口：使用 `proxy_pass` 转发到后端。

---

## 2. 示例配置

```nginx
server {
    listen 80;
    server_name example.com;

    # 静态资源直接由 Nginx 提供
    location ~* \.(jpg|jpeg|png|gif|css|js|ico|html|woff|ttf)$ {
        root /var/www/html;
        expires 7d;
        access_log off;
    }

    # 动态请求转发给后端
    location /api/ {
        proxy_pass http://127.0.0.1:8080;  # 假设后端是 Java/PHP 等
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # 主页或其它请求统一交给后端处理
    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
```

---


## 3. alias 与 root 区别

* `root` 是拼接路径，如：

  ```nginx
  location /static/ {
      root /var/www/html;
  }
  ```

  请求 `/static/img.png` 会读取文件 `/var/www/html/static/img.png`

* `alias` 是替换路径，如：

  ```nginx
  location /static/ {
      alias /var/www/html/;
  }
  ```

  请求 `/static/img.png` 会读取文件 `/var/www/html/img.png`

---


