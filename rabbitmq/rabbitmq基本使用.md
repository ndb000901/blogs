# rabbitmq基本使用

## 1.常用命令

| 功能 | 命令 |
|-------|-------------------------------|
| 启动服务 | `rabbitmq-server -detached` |
| 停止服务 | `rabbitmqctl stop` |
| 查看状态 | `rabbitmqctl status` |
| 查看插件列表 | `rabbitmq-plugins list` |
| 启用插件 | `rabbitmq-plugins enable xxx` |
| 添加用户 | `rabbitmqctl add_user 用户 密码` |
| 设置权限 | `rabbitmqctl set_permissions -p / 用户 ".*" ".*" ".*"` |
| 设置管理员角色 | `rabbitmqctl set_user_tags 用户 administrator` |

## 2.启用 Web 管理控制台

```bash

rabbitmq-plugins enable rabbitmq_management
```

默认 Web 控制台访问地址：

- http://localhost:15672/

默认用户名密码：

- 用户名：guest
- 密码：guest
- 注意：guest 只能本地访问，远程需要创建新用户


## 3.创建用户

```bash

# 添加用户
rabbitmqctl add_user hello hello123456

# 修改密码
rabbitmqctl change_password hello new_password

# 设置用户权限
rabbitmqctl set_permissions -p / hello ".*" ".*" ".*"

# 设置为管理员（可登录 web 界面）
rabbitmqctl set_user_tags hello administrator

# 指定用户的权限
rabbitmqctl list_permissions -p /
```

### 权限详解：

- `set_permissions`：RabbitMQ 控制命令，用于设置某个用户在指定虚拟主机上的权限。

- `-p /`：这个选项指定了虚拟主机（vhost）。在 RabbitMQ 中，虚拟主机（vhost）是一个逻辑上的隔离单位，相当于数据库中的一个 schema。`/` 是默认的虚拟主机（root vhost），它是 RabbitMQ 默认创建的第一个虚拟主机。如果你没有特别创建新的虚拟主机，`/` 就是你要操作的默认虚拟主机。

- `hello`：要设置权限的用户。

- `".*"`：这三个 `".*"` 参数分别代表该用户对指定虚拟主机中 **资源的权限**，其中：
  - 第一个 `".*"` 对应 **configure** 权限，表示该用户可以配置哪些资源（例如交换机、队列等）。
  - 第二个 `".*"` 对应 **write** 权限，表示该用户可以写入消息到队列。
  - 第三个 `".*"` 对应 **read** 权限，表示该用户可以从队列中读取消息。

  `"*"` 是正则表达式，`.*` 表示任意字符零次或多次，因此这表示允许该用户对所有资源（交换机、队列等）具有完全的 **配置**、**写入** 和 **读取** 权限。


## 4.配置文件

[配置文件示例](https://github.com/rabbitmq/rabbitmq-server/blob/v4.0.x/deps/rabbit/docs/rabbitmq.conf.example)