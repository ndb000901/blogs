# wireshark 抓包解密TLS流量

## 1. 获取TLS密钥


### 1.1 Chrome客户端

#### 日志存放路径

```bash

# 选择路径
export SSLKEYLOGFILE=/$HOME/sslkeys.log

```

#### 启动Chrome

```bash

# incognito 以“隐身模式”（Incognito Mode）启动 Chrome 浏览器
google-chrome https://hello.work --incognito

```

### 1.2 curl 客户端

#### 部分版本可直接生成

```bash

export SSLKEYLOGFILE=/path/to/sslkeys.log

curl -v https://hello.work

# 指定最大tls版本
# curl -v https://hello.work --tls-max 1.2

# 查看帮助
# curl --help all | grep tls

# 指定加密套件
# curl -v https://hello.work --ciphers TLS_AES_128_GCM_SHA256
# 查看加密套件列表
# openssl ciphers -v


# 我的版本
curl 7.81.0 (x86_64-pc-linux-gnu) libcurl/7.81.0 OpenSSL/3.0.2 zlib/1.2.11 brotli/1.0.9 zstd/1.4.8 libidn2/2.3.2 libpsl/0.21.0 (+libidn2/2.3.2) libssh/0.9.6/openssl/zlib nghttp2/1.43.0 librtmp/2.3 OpenLDAP/2.5.16
Release-Date: 2022-01-05
Protocols: dict file ftp ftps gopher gophers http https imap imaps ldap ldaps mqtt pop3 pop3s rtmp rtsp scp sftp smb smbs smtp smtps telnet tftp
Features: alt-svc AsynchDNS brotli GSS-API HSTS HTTP2 HTTPS-proxy IDN IPv6 Kerberos Largefile libz NTLM NTLM_WB PSL SPNEGO SSL TLS-SRP UnixSockets zstd
```

#### 若curl 版本不支持可使用 `mitmproxy`(会将TLS密钥生成到SSLKEYLOGFILE) 做代理


[mitmproxy官网](https://mitmproxy.org/)

```bash

# 启动 mitmproxy
mitmproxy --listen-port 8888 --listen-host 127.0.0.1 --mode regular --ssl-insecure

# 使用curl 访问
curl -v -x http://127.0.0.1:8888 https://hello.work -k
```

#### mitmproxy 参数
- `--listen-port 8888`: 指定代理服务监听的端口为 `8888`，即你将通过这个端口来访问 `mitmproxy`。

- `--listen-host 127.0.0.1`: 指定 `mitmproxy` 只监听来自本地计算机（`127.0.0.1`，也就是 `localhost`）的请求。这意味着只能从同一台机器发起请求来连接到 `mitmproxy`。

- `--mode regular`: 设置代理模式为常规代理模式，即正常地进行中间人代理操作，拦截和转发所有 HTTP/HTTPS 流量。

- `--ssl-insecure`: 启用此参数后，`mitmproxy` 将会忽略 SSL/TLS 证书的验证错误。这在进行 HTTPS 流量拦截时很有用，因为 `mitmproxy` 会使用自签名证书替代目标网站的证书，正常情况下浏览器和客户端会报证书错误，`--ssl-insecure` 会允许通过这些错误进行代理。


mitmproxy 自签名证书在 `~/.mitmproxy/`

---

#### curl 参数
- `-v`: 启用详细输出模式（verbose），这会打印更多的信息，例如请求和响应的头部，TLS 握手过程等。通过这个选项可以看到请求和响应的详细信息，便于调试。

- `-x http://127.0.0.1:8888`: 这个选项指定了 `curl` 使用 HTTP 代理，代理地址是 `127.0.0.1:8888`。也就是通过本地的 `mitmproxy` 进行所有的 HTTP/HTTPS 请求。

- `https://hello.work`: 这是你要访问的目标网站。在使用 `curl` 时，`https://` 表示这是一个 HTTPS 请求。

- `-k`: 这个选项告诉 `curl` 忽略 SSL 证书验证错误。正常情况下，`curl` 会验证 SSL 证书，如果证书无效或被篡改，`curl` 会拒绝连接。使用 `-k` 可以忽略证书的错误，通常用于开发和调试环境，特别是当你使用自签名证书（例如 `mitmproxy` 的证书）时。

---




## 2. 在wireshark 查看抓包的TLS流量

```text

Wireshark → Preferences → Protocols → TLS

找到 Pre-Master Secret log filename，填入设定的日志路径
```

