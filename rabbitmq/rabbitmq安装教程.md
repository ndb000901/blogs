# rabbitmq安装教程

## 1.下载

[下载页面](https://www.rabbitmq.com/docs/install-generic-unix)

```bash

wget -c https://github.com/rabbitmq/rabbitmq-server/releases/download/v4.0.7/rabbitmq-server-generic-unix-4.0.7.tar.xz
```

## 2.解压

```bash

tar -xvf rabbitmq-server-generic-unix-4.0.7.tar.xz
```

## 3.配置环境变量

```bash

# vim ~/.zshrc 添加环境变量
export PATH=$PATH:/home/hello/local/rabbitmq_server-4.0.7/sbin

# 刷新
source ~/.zshrc
```

## 4.使用

[rabbitmq基本使用](./rabbitmq基本使用.md)

