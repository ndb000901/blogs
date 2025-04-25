# kubectl 安装教程

kubectl 版本和集群版本之间的差异必须在一个小版本号内。 例如：v1.33 版本的客户端能与 v1.32、 v1.33 和 v1.34 版本的控制面通信。 用最新兼容版的 kubectl 有助于避免不可预见的问题。

[官方教程](https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-linux/)

## 1. 下载

```bash

curl -LO https://dl.k8s.io/release/v1.33.0/bin/linux/amd64/kubectl
```

## 2. 添加环境变量


```bash
# 添加可执行权限

chmod +x kubectl

# vim ~/.zshrc
export PATH=$PATH:/home/hello/local/kubectl

# 刷新
source ~/.zshrc
```