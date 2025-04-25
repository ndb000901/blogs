# pushgateway 使用教程

## 1. **Web 接口相关**

| 参数 | 说明 |
|------|------|
| `--web.listen-address=:9091` | 指定监听地址和端口。可以重复使用多次监听多个地址。示例：`:9091`, `[::1]:9091`。|
| `--web.systemd-socket` | 使用 systemd 的 socket 激活而不是传统端口监听（仅 Linux）。适合 systemd 管理部署。|
| `--web.config.file=""` | 提供一个 web 配置文件（支持 TLS / basic auth）。由 `exporter-toolkit` 项目实现。|
| `--web.telemetry-path="/metrics"` | 指定用于导出指标的路径，默认是 `/metrics`。|
| `--web.external-url=` | 指定 Pushgateway 在外部访问时使用的 URL（通常用于反向代理设置）。|
| `--web.route-prefix=""` | 内部路由的前缀路径，默认等于 `--web.external-url` 中的路径部分。|

---

## 2. **功能控制**

| 参数 | 说明 |
|------|------|
| `--[no-]web.enable-lifecycle` | 启用 HTTP 接口（如 `/-/quit`）用于优雅关闭（默认关闭）。|
| `--[no-]web.enable-admin-api` | 启用管理 API，比如删除 job 或 group（默认关闭）。|

---

## 3. **持久化配置**

| 参数 | 说明 |
|------|------|
| `--persistence.file=""` | 设置数据持久化路径。如果为空，仅存储在内存中，重启丢失数据。|
| `--persistence.interval=5m` | 设置将内存中数据写入文件的间隔时间，默认为 5 分钟。|

---

## 4. **推送行为控制**

| 参数 | 说明 |
|------|------|
| `--[no-]push.disable-consistency-check` | 关闭一致性检查（可能不安全）。默认开启一致性校验，即一个 job 的不同推送必须 label 结构一致。|
| `--[no-]push.enable-utf8-names` | 允许 metric 名称或 label 使用 UTF-8 字符（默认仅 ASCII，推荐保持关闭）。|

---

## 5. **日志相关**

| 参数 | 说明 |
|------|------|
| `--log.level=info` | 设置日志等级。可选：`debug`, `info`, `warn`, `error`。|
| `--log.format=logfmt` | 设置日志输出格式。可选：`logfmt`（默认）或 `json`。|

---


## 6. 示例启动方式（整合参数）

```bash

./pushgateway \
  --web.listen-address="192.168.43.242:9091" \
  --web.enable-admin-api \
  --web.enable-lifecycle \
  --persistence.file="/data/pushgateway.data" \
  --persistence.interval=1m \
  --log.level=info
```

---
