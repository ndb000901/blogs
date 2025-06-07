# open-webui 搭建

[官网]()


## 1. docker 部署

`compose.yml`

```yml

version: '3.9'
services:
  svc-open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: svc.open-webui
    restart: always
#    ports:
#      - "3000:8080"
    volumes:
      - open-webui:/app/backend/data
    extra_hosts:
      - "host.docker.internal:host-gateway"
    networks:
      - net.svc
networks:
  net.svc:
    driver: bridge
    external:
      name: net.svc
volumes:
  open-webui:
```
