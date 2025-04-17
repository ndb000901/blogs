# Nacos 安装教程

## 1. 下载

[下载页面](https://github.com/alibaba/nacos/releases)
```bash

wget -c https://github.com/alibaba/nacos/releases/download/2.5.1/nacos-server-2.5.1.tar.gz
```


## 2. 解压

```bash
tar -zxvf nacos-server-2.5.1.tar.gz
```

## 3. 添加环境变量

```bash

# vim ~/.zshrc
export PATH=$PATH:/home/hello/local/nacos/bin

# 刷新
source ~/.zshrc
```

## 4.使用

[Nacos基础使用](./Nacos基础使用.md)