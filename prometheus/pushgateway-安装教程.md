# pushgateway 安装教程

## 1. 下载

[下载页面](https://prometheus.io/download/)

```bash

wget https://github.com/prometheus/pushgateway/releases/download/v1.11.1/pushgateway-1.11.1.linux-amd64.tar.gz
```

## 2. 解压

```bash

tar -zxvf pushgateway-1.11.1.linux-amd64.tar.gz
```

## 3.配置环境变量

```bash

# vim ~/.zshrc
export PATH=$PATH:/home/hello/local/pushgateway-1.11.1

# 刷新
source ~/.zshrc
```

## 4. 启动

[使用教程](./pushgateway-使用教程.md)

```bash

/home/hello/local/pushgateway-1.11.1/pushgateway \
  --web.listen-address="192.168.43.242:9091" \
  --web.enable-admin-api \
  --web.enable-lifecycle \
  --persistence.file="/home/hello/local/pushgateway-1.11.1/pushgateway.data" \
  --persistence.interval=1m \
  --log.level=info
```

### 访问web页面

```bash

http://192.168.43.242:9091/
```

## 5. 配置 `prometheus.yml`

```yml
scrape_configs:
  - job_name: "pushgateway"
    static_configs:
      - targets: ["localhost:9091"]

```

## 6. 测试

```bash

cat <<EOF | curl --data-binary @- http://192.168.43.242:9091/metrics/job/test-pushgateway/instance/instance-1
# TYPE some_metric counter
some_metric{label1="value1"} 42
# TYPE another_metric gauge
another_metric 3.14
EOF

```

- 可访问 `pushgateway` http://192.168.43.242:9091/ 查看
- 可访问 `prometheus` http://192.168.43.242:9090/query 查看