# Sentinel 安装教程

## 1. 下载

[下载页面](https://github.com/alibaba/Sentinel/releases)

```bash

wget -c https://github.com/alibaba/Sentinel/releases/download/1.8.8/sentinel-dashboard-1.8.8.jar
```

## 2. 启动

```bash

java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```