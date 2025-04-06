# Lua 安装教程

## 1. 下载

[下载页面](https://www.lua.org/download.html)

```bash

wget -c https://www.lua.org/ftp/lua-5.4.7.tar.gz
```

## 2. 解压

```bash

tar -zxvf lua-5.4.7.tar.gz
```

## 3. 编译安装

```bash

make
make INSTALL_TOP=/home/hello/local/lua-5.4.7/out install

```


## 4. 配置环境变量

```bash

# vim ~/.zshrc 或 ~/.bashrc 添加环境变量
export PATH=$PATH:/home/hello/local/lua-5.4.7/out/bin

# source ~/.zshrc
```

## 5. VSCode 插件

[Lua](https://marketplace.visualstudio.com/items?itemName=sumneko.lua)

[Lua](https://marketplace.visualstudio.com/items?itemName=yinfei.luahelper)