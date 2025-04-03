# Nginx Docker部署

## 1.拉取镜像

```bash

docker pull nginx:1.27.4
```

## 2.compose.yml

```bash

name: coms-nginx # 项目名称
services:
  app:
    container_name: coms-nginx # 容器名称
    image: nginx:1.27.4 # 镜像
    restart: unless-stopped # 重启
    ports: # 端口
      - "443:443"
      - "80:80"
    volumes: # 挂载目录
      - ./conf:/etc/nginx:ro
      - ./html:/usr/share/nginx/html:rw
    networks:
      coms.hello.com: {}
networks:
  coms.hello.com:
    driver: bridge
    external:
      name: coms.hello.com
```