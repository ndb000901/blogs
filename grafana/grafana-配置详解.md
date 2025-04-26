# grafana 配置详解

[官方配置文档](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/)

## 1. 配置文件位置

- **默认配置文件**：`defaults.ini` 位于 `<GRAFANA_HOME>/conf/defaults.ini`，包含所有默认设置。**不建议修改**，因为升级时可能会被覆盖。

- **自定义配置文件**：`grafana.ini` 或 `custom.ini`（取决于安装方式），用于覆盖默认设置。

    - **Linux（deb/rpm 安装）**：`/etc/grafana/grafana.ini`
    - **Windows**：`<GRAFANA_HOME>\conf\custom.ini`
    - **Docker**：通过挂载自定义配置文件到容器内的 `/etc/grafana/grafana.ini` 实现。

---

## 2. 配置段落详解

### [paths] 路径设置

- `data`：Grafana 用于存储数据库、会话等数据的目录。
- `logs`：日志文件存储路径。
- `plugins`：插件存储路径。
- `provisioning`：配置自动化（如数据源、仪表板）的目录。

### [server] 服务器设置

- `http_addr`：监听地址，默认 `0.0.0.0`（所有接口）。
- `http_port`：监听端口，默认 `3000`。
- `domain`：用于生成链接的域名。
- `enforce_domain`：是否强制使用上述域名。
- `root_url`：Grafana 的根 URL，影响链接生成。
- `serve_from_sub_path`：是否从子路径提供服务。

### [database] 数据库设置

- `type`：数据库类型，默认 `sqlite3`。
- `host`：数据库主机地址。
- `name`：数据库名称。
- `user`：数据库用户名。
- `password`：数据库密码。
- `ssl_mode`：SSL 模式。

### [security] 安全设置

- `admin_user`：管理员用户名，默认 `admin`。
- `admin_password`：管理员密码，默认 `admin`。
- `secret_key`：用于加密的密钥。
- `disable_gravatar`：是否禁用 Gravatar。
- `cookie_secure`：是否启用安全 Cookie。

### [auth] 认证设置

- `disable_login_form`：是否禁用登录表单。
- `disable_signout_menu`：是否禁用登出菜单。
- `signout_redirect_url`：登出后的重定向 URL。

### [auth.anonymous] 匿名访问设置

- `enabled`：是否启用匿名访问。
- `org_name`：匿名用户所属组织名称。
- `org_role`：匿名用户的角色。

### [users] 用户设置

- `allow_sign_up`：是否允许用户注册。
- `auto_assign_org`：是否自动分配组织。
- `auto_assign_org_role`：自动分配的角色。
- `default_theme`：默认主题（`dark`、`light`、`system`）。

### [smtp] 邮件设置

- `enabled`：是否启用 SMTP。
- `host`：SMTP 服务器地址。
- `user`：SMTP 用户名。
- `password`：SMTP 密码。
- `from_address`：发件人地址。
- `from_name`：发件人名称。 

### [alerting] 告警设置

- `enabled`：是否启用告警功能。
- `execute_alerts`：是否执行告警。
- `error_or_timeout`：错误或超时的处理方式。
- `nodata_or_nullvalues`：无数据或空值的处理方式。

### [dashboards] 仪表板设置

- `versions_to_keep`：保留的仪表板版本数量。
- `min_refresh_interval`：最小刷新间隔。

### [log] 日志设置

- `mode`：日志模式（`console`、`file`）。
- `level`：日志级别（`debug`、`info`、`warn`、`error`）。
- `filters`：日志过滤器。

### [metrics] 指标设置

- `enabled`：是否启用指标。
- `interval_seconds`：指标收集间隔（秒）。

### [analytics] 分析设置

- `reporting_enabled`：是否启用匿名使用统计。
- `check_for_updates`：是否检查更新。

### [plugins] 插件设置

- `enable_alpha`：是否启用 alpha 插件。
- `app_tls_skip_verify_insecure`：是否跳过插件的 TLS 验证。

---

## 3. 配置优先级

1. **环境变量**：如 `GF_SECURITY_ADMIN_PASSWORD=secret`。
2. **命令行参数**：如 `grafana-server --config=/etc/grafana/grafana.ini`。
3. **配置文件**：`grafana.ini` 或 `custom.ini`。
4. **默认配置**：`defaults.ini`。 


