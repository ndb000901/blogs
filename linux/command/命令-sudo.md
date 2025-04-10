# sudo 命令

`sudo`（**Super User DO**）是 Linux/Unix 系统中允许普通用户以 **其他用户（默认是 root）身份执行命令** 的工具。它是权限控制和安全管理的重要机制。

---

## 1. 基本命令格式

```bash

sudo [选项] <命令>
```

- 默认以 `root` 身份执行指定命令。
- 系统会提示输入当前用户的密码（不是 root 的密码）。

---

## 2. 常用选项

| 选项 | 说明 |
|------|------|
| `-u <user>` | 指定以哪个用户身份执行命令（默认是 root） |
| `-k` | 立刻失效缓存的 sudo 权限（强制下次输入密码） |
| `-v` | 更新 sudo 会话有效期（不执行命令） |
| `-l` | 显示当前用户可以使用的 sudo 权限 |
| `-h` | 显示帮助信息 |
| `-s` | 使用当前 shell 执行命令 |
| `-i` | 打开一个模拟登录环境的新 shell（类似 `su -`） |
| `--reset-timestamp` | 重置 sudo 的时间戳（立刻要求再次输入密码） |

---

## 3. 使用示例

### 3.1 以 root 身份执行命令

```bash

sudo apt update
```
提示输入当前用户密码，成功后以 root 权限执行 `apt update`。

---

### 3.2 以其他用户身份执行命令

```bash

sudo -u www-data whoami
```
以 `www-data` 用户身份运行 `whoami`。

---

### 3.3 打开 root shell（不推荐长期使用）

```bash

sudo -i
```
或者

```bash

sudo su
```
你将进入一个 root shell 环境，直到退出。

---

### 3.4 查看当前 sudo 权限

```bash

sudo -l
```
列出你当前可运行的命令和权限配置。

---

### 3.5 强制 sudo 下次执行时重新输入密码

```bash

sudo -k
```

---

### 3.6 用 sudo 编辑系统文件（推荐用 visudo 修改 sudo 配置）

```bash
sudo nano /etc/hosts
```

---

## 4. sudoers 配置文件（/etc/sudoers）

- 使用 `visudo` 命令进行编辑，防止语法错误导致系统无法授权。

```bash

sudo visudo
```

示例配置：
```bash

username ALL=(ALL) ALL
```

表示该用户在任意主机、以任意用户身份，可以运行任意命令。

---

## 5. 安全机制

- sudo 默认有一个 **5 分钟的授权缓存**，5 分钟内再次运行不需输入密码。
- 可以通过配置 `/etc/sudoers` 精细控制哪些命令可用。
- 日志通常记录在 `/var/log/auth.log` 或 `journalctl` 中，便于审计。

---



## 6. 原理


### 6.1 读取配置文件 `/etc/sudoers`
- `sudo` 根据 `/etc/sudoers`（或 `/etc/sudoers.d/`）判断当前用户是否有权限运行该命令。
- 配置文件通常通过 `visudo` 来安全编辑。

### 6.2 检查用户是否在 sudo 白名单中
- 如果没有权限，直接报错。
- 有权限则继续执行。

### 6.3 检查是否需要输入密码
- 默认需要输入当前用户的密码（不是 root 的密码）。
- 密码验证通过后，会在 5 分钟内缓存（可在 `/etc/sudoers` 里配置 `timestamp_timeout`）。

### 6.4 提升权限并执行命令
- `sudo` 会使用 `setuid(0)` 提升为 root（或指定用户）权限。
- 然后执行目标命令。

### 6.5 日志记录
- `sudo` 会记录谁执行了什么命令，记录位置通常是：
    - `/var/log/auth.log`（Debian/Ubuntu）
    - `/var/log/secure`（RHEL/CentOS）

---




