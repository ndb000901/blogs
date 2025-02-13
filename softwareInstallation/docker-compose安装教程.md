# docker-compose安装教程

## 1.下载

[下载地址](https://github.com/docker/compose/releases)

```bash

wget xxxxxxx
```

## 2.添加环境变量

```bash

# 可执行文件在/home/hello/local/docker-compose目录
# 编辑～/.zshrc 文件，添加环境变量
export PATH=$PATH:/home/hello/local/docker-compose

# 刷新
source ~/.zshrc
```