# 命令 curl

`curl`（Client URL）是一个强大的命令行工具，用于在命令行中发送各种协议的请求（如 HTTP、HTTPS、FTP、SFTP 等），常用于调试接口、下载资源、API 测试等。

---

## 1. 基本格式

```bash

curl [options] [URL]
```

例如：

```bash

curl https://example.com
```

---

## 2. 常用选项

### 2.1 `-X`：指定 HTTP 请求方法

```bash

curl -X POST https://example.com
```

- 指定请求方法为 `POST`，默认是 `GET`。

---

### 2.2 `-d`：发送请求体数据（POST 提交表单）

```bash

curl -d "name=Tom&age=22" https://example.com/api
```

- 通常用于 `POST` 请求，`-X POST` 可以省略，`curl` 会自动判断。

---

### 2.3 `-H`：添加请求头（Header）

```bash

curl -H "Content-Type: application/json" https://example.com
```

- 可重复添加多个 `-H` 以添加多个请求头。

---

### 2.4 `-o`：将输出保存为文件

```bash

curl -o file.html https://example.com
```

- 下载内容保存为 `file.html`。

---

### 2.5 `-O`：使用原始文件名保存

```bash

curl -O https://example.com/file.zip
```

- 自动使用 URL 中的文件名进行保存。

---

### 2.6 `-I`：仅获取响应头（HEAD 请求）

```bash

curl -I https://example.com
```

- 不获取内容，仅查看服务器响应头部。

---

### 2.7 `-L`：跟随重定向

```bash

curl -L https://short.url
```

- 若服务器返回 3xx 重定向，`-L` 让 `curl` 自动跟随。

---

### 2.8 `-u`：设置 HTTP Basic Auth 认证

```bash

curl -u user:password https://example.com
```

- 向受保护的接口发送请求。

---

### 2.9 `-k`：忽略 SSL 证书验证

```bash

curl -k https://self-signed.badssl.com
```

- 忽略证书错误，常用于测试 HTTPS 接口。

---

### 2.10 `-s`：静默模式（不显示进度）

```bash

curl -s https://example.com
```

- 不显示进度条，只输出内容。

---

### 2.11 `-v`：显示详细的请求响应过程

```bash

curl -v https://example.com
```

- 用于调试，展示完整请求过程（包括 header、TLS 握手等）。

---

### 2.12 `--compressed`：自动发送 `Accept-Encoding: gzip` 并自动解压响应

```bash

curl --compressed https://example.com
```

- 自动使用 gzip 压缩传输（节省带宽）。

---

### 2.13 `-e`：模拟 Referer 来源页

```bash

curl -e "https://from.com" https://target.com
```

---

### 2.14 `-A`：模拟 User-Agent

```bash

curl -A "Mozilla/5.0 Chrome" https://example.com
```

---

### 2.15 `-b` 与 `-c`：模拟 Cookie 发送/接收

```bash

curl -b "key=value" https://example.com
curl -c cookie.txt https://example.com
```

- `-b` 用于发送 Cookie，`-c` 用于接收并保存 Cookie。

### 2.15 手动指定某个域名在访问时解析到指定的 IP 地址（绕过 DNS 解析）

```bash

curl -v --resolve hello.work:443:192.168.43.242 https://hello.work
```
---

## 3. 示例

### GET 请求带参数：

```bash

curl "https://api.example.com/user?id=1"
```

### POST 请求 JSON 数据：

```bash

curl -X POST -H "Content-Type: application/json" \
     -d '{"username": "admin", "password": "123"}' \
     https://api.example.com/login
```

### 下载并保存文件：

```bash

curl -O https://example.com/file.zip
```

### 模拟浏览器访问：

```bash

curl -A "Mozilla/5.0" https://example.com
```

---

### 上传文件（表单类型）

```bash

curl -F "file=@test.jpg" https://example.com/upload
```

### 上传 JSON 文件

```bash

curl -H "Content-Type: application/json" -d @data.json https://example.com/api
```

---
