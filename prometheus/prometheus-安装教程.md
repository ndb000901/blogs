# prometheus 安装教程


## 1. 下载

[下载页面](https://prometheus.io/download/)

```bash

wget https://github.com/prometheus/prometheus/releases/download/v3.3.0/prometheus-3.3.0.linux-amd64.tar.gz
```

## 2. 解压

```bash

tar -zxvf prometheus-3.3.0.linux-amd64.tar.gz
```

## 3. 配置环境变量

```bash

# vim ~/.zshrc

export PATH=$PATH:/home/hello/local/prometheus-3.3.0

# 刷新
source ~/.zshrc
```

## 4. 启动

```bash

./prometheus --config.file=prometheus.yml
```

### 查看prometheus自身指标

```bash

curl http://192.168.43.242:9090/metrics
```

### 访问web页面

```bash

http://192.168.43.242:9090/graph
```

## 5. 配置

[配置详解](./prometheus-配置详解.md)

```yml
# 全局配置，全局的抓取频率和规则评估频率
global:
  scrape_interval: 15s           # 设置抓取频率为每 15 秒（默认是 1 分钟）
  evaluation_interval: 15s       # 设置规则评估频率为每 15 秒（默认是 1 分钟）
  # scrape_timeout 使用默认值（10 秒）

# Alertmanager 配置（用于告警通知）
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093   # 这里可以填写 Alertmanager 的地址，目前被注释掉了

# 规则文件列表，会定期加载并根据 global.evaluation_interval 进行评估
rule_files:
  # - "first_rules.yml"         # 这里可以填写 Prometheus 规则文件路径，目前被注释掉了
  # - "second_rules.yml"

# 抓取配置部分 scrape_configs
# 每个抓取配置都可以定义一个或多个目标地址
scrape_configs:
  # 这是一个 job 名称为 "prometheus" 的抓取配置
  - job_name: "prometheus"       # 抓取任务名称，对应指标中的 job 标签

    # metrics_path 默认为 '/metrics'
    # scheme 默认为 'http'

    static_configs:              # 静态配置目标地址（直接写死 IP:PORT）
      - targets: ["192.168.43.242:9090"]   # Prometheus 抓取指标的地址（通常是被监控的服务）
        labels:
          app: "prometheus"      # 自定义标签，会加在该目标抓取的所有时序数据上（如 app=prometheus）

```