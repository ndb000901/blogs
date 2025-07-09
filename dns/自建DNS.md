# 自建DNS服务

解决频繁切换hosts, 过于痛苦


## 1. 目录结构

```text
.
├── cert
│   ├── dns.crt
│   ├── dns.key
│   └── gen-cert.sh
├── compose.yml
└── conf
    ├── pre
    │   └── Corefile
    └── test
        └── Corefile

```

## 2. `compose.yml`

```yml
services:
  # 测试环境
  coredns-test:
    image: coredns/coredns:1.12.2
    container_name: coredns-test
    volumes:
      - ./conf/test:/etc/coredns
    working_dir: /etc/coredns
    ports:
      - "10053:53/udp"    # 避免占用宿主机 53 端口
    restart: always

  dnsproxy-test:
    image: adguard/dnsproxy:v0.76.1
    container_name: dnsproxy-test
    depends_on:
      - coredns-test
    volumes:
      - ./cert:/cert
    command: >
      --https-port=3000
      --tls-crt=/cert/dns.crt
      --tls-key=/cert/dns.key
      --upstream=coredns-test:53
      --verbose
    ports:
      - "3000:3000"
    restart: always
  # 预发布环境
  coredns-pre:
    image: coredns/coredns:1.12.2
    container_name: coredns-pre
    volumes:
      - ./conf/pre:/etc/coredns
    working_dir: /etc/coredns
    ports:
      - "10063:53/udp"    # 避免占用宿主机 53 端口
    restart: always

  dnsproxy-pre:
    image: adguard/dnsproxy:v0.76.1
    container_name: dnsproxy-pre
    depends_on:
      - coredns-pre
    volumes:
      - ./cert:/cert
    command: >
      --https-port=3000
      --tls-crt=/cert/dns.crt
      --tls-key=/cert/dns.key
      --upstream=coredns-pre:53
      --verbose
    ports:
      - "4000:3000"
    restart: always
```

## 2. `gen-cert.sh`

使用该脚本，生成证书/与RSA私钥

```bash
#!/bin/bash
# 换成自己喜欢的域名
openssl req -x509 -newkey rsa:4096 -nodes -days 3650 \
  -keyout dns.key -out dns.crt \
  -subj "/CN=dns.hello.work" \
  -addext "subjectAltName=DNS:dns.hello.work"

```

## 3. 测试环境

`conf/test/Corefile`

```ini
. {
    hosts {
        # 测试
        10.9.22.52 apis.test.com
        fallthrough
    }
    forward . 8.8.8.8 1.1.1.1
    log
    errors
}

```


## 4. 预发布环境

```ini
. {
    hosts {
        # 预发布
        2.2.2.2 apis.pre.com
        fallthrough
    }
    forward . 8.8.8.8 1.1.1.1
    log
    errors
}

```


## 5. 启动

```sh
docker-compose up -d
```

## 6.配置DoH

测试：
https://dns.hello.work:3000/dns-query

预发布: 
https://dns.hello.work:4000/dns-query