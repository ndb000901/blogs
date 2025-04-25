# minikube 安装教程

## 1. 下载


[下载页面](https://github.com/kubernetes/minikube/releases)

```bash

wget https://github.com/kubernetes/minikube/releases/download/v1.35.0/minikube-linux-amd64.tar.gz
```

## 2. 解压

```bash

tar -zxvf minikube-linux-amd64.tar.gz
# 解压出来是out目录，我改成了minikube
```

## 3. 环境变量

```bash

# vim ~/.zshrc

export PATH=$PATH:/home/hello/local/minikube

# 刷新
source ~/.zshrc
```

## 4. 启动

```bash

```
