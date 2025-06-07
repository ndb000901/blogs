# Nginx示例-HLS流媒体服务器

[参考文档](../systemDesign/hls/基于HLS流媒体防盗系统设计.md)

## 1. Nginx配置示例

```conf
server {
        listen       443 ssl;
        server_name  hls.hello.work;

        ssl_certificate      certs/hls.hello.work.fullchain.pem;
        ssl_certificate_key  certs/hls.hello.work.key.pem;

        #    ssl_session_cache    shared:SSL:1m;
        #    ssl_session_timeout  5m;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;


        location /hls {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /usr/share/nginx/html;  # 注意后面会放 m3u8 和 ts 文件
            add_header Cache-Control no-cache;
	        # 添加 CORS 头部
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
            add_header Access-Control-Allow-Headers "DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range";
            add_header Access-Control-Expose-Headers "Content-Length,Content-Range";
        }
    }
```

## 2. 文件结构

```text
hls
├── demo
│   ├── base
│   │   ├── demo0.ts
│   │   ├── demo10.ts
│   │   ├── demo11.ts
│   │   ├── demo12.ts
│   │   ├── demo13.ts
│   │   ├── demo14.ts
│   │   ├── demo15.ts
│   │   ├── demo16.ts
│   │   ├── demo17.ts
│   │   ├── demo18.ts
│   │   ├── demo19.ts
│   │   ├── demo1.ts
│   │   ├── demo20.ts
│   │   ├── demo21.ts
│   │   ├── demo22.ts
│   │   ├── demo23.ts
│   │   ├── demo24.ts
│   │   ├── demo25.ts
│   │   ├── demo26.ts
│   │   ├── demo27.ts
│   │   ├── demo28.ts
│   │   ├── demo29.ts
│   │   ├── demo2.ts
│   │   ├── demo30.ts
│   │   ├── demo3.ts
│   │   ├── demo4.ts
│   │   ├── demo5.ts
│   │   ├── demo6.ts
│   │   ├── demo7.ts
│   │   ├── demo8.ts
│   │   ├── demo9.ts
│   │   └── demo.m3u8
│   ├── demo.mov
│   └── hello-123456
│       ├── demo0.ts
│       ├── demo3.ts
│       └── hello-123456.m3u8
└── demo-aes
    ├── auth-demo
    │   ├── auth-demo.m3u8
    │   └── demo0.ts
    ├── base
    │   ├── demo0-raw.ts
    │   ├── demo0.ts
    │   ├── demo10.ts
    │   ├── demo11.ts
    │   ├── demo12.ts
    │   ├── demo13.ts
    │   ├── demo14.ts
    │   ├── demo15.ts
    │   ├── demo16.ts
    │   ├── demo17.ts
    │   ├── demo18.ts
    │   ├── demo19.ts
    │   ├── demo1.ts
    │   ├── demo20.ts
    │   ├── demo21.ts
    │   ├── demo22.ts
    │   ├── demo23.ts
    │   ├── demo24.ts
    │   ├── demo25.ts
    │   ├── demo26.ts
    │   ├── demo27.ts
    │   ├── demo28.ts
    │   ├── demo29.ts
    │   ├── demo2.ts
    │   ├── demo30.ts
    │   ├── demo3.ts
    │   ├── demo4.ts
    │   ├── demo5.ts
    │   ├── demo6.ts
    │   ├── demo7.ts
    │   ├── demo8.ts
    │   ├── demo9.ts
    │   └── demo.m3u8
    ├── demo-aes.mov
    ├── enc.key
    ├── enc.keyinfo
    └── user-987654
        ├── 2d454e444c4953540a.m3u8 -> user-987654.m3u8
        ├── demo0-raw.ts
        ├── demo0.ts
        └── user-987654.m3u8
```

## 3. 访问

```curl
https://hls.hello.work/hls/demo/base/demo.m3u8
```
