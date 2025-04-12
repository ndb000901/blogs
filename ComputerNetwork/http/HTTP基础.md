# HTTP基础

HTTP（HyperText Transfer Protocol，超文本传输协议）是**Web 的基础通信协议**，用于客户端（通常是浏览器）和服务器之间传输文本、图片、音频、视频等数据。

---

## 资料

- [wireshark下载](https://www.wireshark.org/download.html)
- [数据包下载](./packet/http1.1-demo.pcapng)
- `packet/http1.1-demo.pcapng` 使用wireshark 打开


## 1. HTTP 协议发展历史

| 版本     | 特性概览 |
|----------|----------|
| HTTP/0.9 | 只有 GET 请求，响应无头部，仅支持 HTML 文本 |
| HTTP/1.0 | 支持请求头/响应头、状态码、多种方法（GET/POST），默认短连接 |
| HTTP/1.1 | 持久连接（Keep-Alive）、管道化请求、缓存机制、分块传输 |
| HTTP/2   | 二进制帧、多路复用、头部压缩（HPACK）、服务器推送 |
| HTTP/3   | 基于 QUIC 协议（UDP），0-RTT 建连、更高性能、更少队头阻塞 |

---

## 2. HTTP 报文结构

### 2.1 请求报文（Request）

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: curl/7.68.0
Accept: text/html
...

[可选：请求体，如 POST 的 form 数据或 JSON]
```

- **请求行**：方法 + 路径 + 协议版本
- **请求头**：键值对，传递客户端信息、参数等
- **空行**：头部结束标志
- **请求体**：仅部分方法使用，如 POST、PUT

### 2. 响应报文（Response）

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1024
Cache-Control: no-cache
...

[响应体，如 HTML 页面或 JSON 数据]
```

- **状态行**：版本号 + 状态码 + 原因短语
- **响应头**：描述响应体和服务器信息
- **空行**：头部结束标志
- **响应体**：HTML、JSON、图片等内容

---

## 3. 常用 HTTP 方法（Method）

| 方法   | 说明 |
|--------|------|
| GET    | 获取资源，幂等 |
| POST   | 提交数据，创建资源 |
| PUT    | 更新整个资源，幂等 |
| PATCH  | 局部更新 |
| DELETE | 删除资源，幂等 |
| HEAD   | 类似 GET，但只返回头部 |
| OPTIONS| 查询支持的方法 |
| CONNECT| 建立隧道，例如 HTTPS 代理 |
| TRACE  | 回显请求，调试用 |

---

## 4. 常见状态码（Status Code）

### 1xx 信息性
- `100 Continue`：客户端应继续请求

### 2xx 成功
- `200 OK`：请求成功
- `201 Created`：资源已创建（POST/PUT）
- `204 No Content`：成功但无返回体

### 3xx 重定向
- `301 Moved Permanently`：永久重定向
- `302 Found`：临时重定向
- `304 Not Modified`：协商缓存命中

### 4xx 客户端错误
- `400 Bad Request`：语法错误
- `401 Unauthorized`：未认证（需要登录）
- `403 Forbidden`：已认证但无权限
- `404 Not Found`：资源不存在

### 5xx 服务端错误
- `500 Internal Server Error`：服务异常
- `502 Bad Gateway`：网关错误
- `503 Service Unavailable`：服务不可用

---

## 5. 连接机制

### 5.1 短连接 vs 长连接
- **HTTP/1.0**：默认短连接
- **HTTP/1.1**：默认启用长连接（`Connection: keep-alive`）

### 5.2 管道化（HTTP/1.1）
- 多个请求一起发送，不等响应（但顺序返回）

### 5.3 多路复用（HTTP/2）
- 单个连接并发多个请求响应，无需等待前一个完成（解决队头阻塞）

---

## 6. 缓存机制

### 1. 强缓存
- 响应头：`Cache-Control: max-age=3600`、`Expires`

### 2. 协商缓存
- `ETag` 与 `If-None-Match`
- `Last-Modified` 与 `If-Modified-Since`

客户端根据响应头保存缓存信息，再次请求时携带条件字段，如果资源未修改则服务器返回 `304 Not Modified`。

---

## 7. 安全性：HTTPS

- **HTTPS = HTTP + TLS**
- 提供：
    - 加密（防窃听）
    - 身份认证（服务器/客户端）
    - 完整性保护（防篡改）


## 8. 内容协商机制

- `Accept`: 指定客户端支持的内容类型（如 `application/json`）
- `Accept-Encoding`: 支持的压缩格式（如 `gzip`, `br`）
- `Accept-Language`: 语言偏好（如 `zh-CN`）

---

## 9. HTTP/2 特性

- 二进制协议（非文本）
- 多路复用（stream）
- 头部压缩（HPACK）
- 服务器推送（Server Push）
- 更少连接、更高并发

---

## 10. HTTP/3

- 基于 UDP 的 QUIC 协议
- 连接迁移（IP变动不掉线）
- 0-RTT 握手（更快建连）
- 天生多路复用、无 HOL 阻塞
- 加密集成在传输层（TLS 1.3）


