# git基础-配置

## 1.配置级别

- 1.系统级别配置：`git config --system`
  配置文件：`/etc/gitconfig`
- 2.用户级别配置：`git config --global`
  配置文件：`~/.gitconfig`
- 3.仓库级别配置：`git config --local`
  配置文件：`.git/config`

配置优先级：系统级别配置 \< 用户级别配置 \< 仓库级别配置

每一个级别覆盖上一级别的配置

## 2.配置用户信息

```bash

git config --global user.name "hello"
git config --global user.email "hello@gmail.com"
```

## 3.配置编辑器

当git需要你输入信息时，会调用编辑器

```bash

# emacs
git config --global core.editor emacs

# vim
git config --global core.editor vim
```

## 4.检查配置

```bash

git config --list
```

## 5.获取帮助

```bash

git help <verb>
git <verb> --help
man git-<verb>
```

## 6.配置代理

ssh 代理，修改配置文件：`~/.ssh/config`

```bash
# 必须是 github.com
Host github.com
HostName github.com
User git
# 走 HTTP 代理
# ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=8080
# 走 socks5 代理
ProxyCommand nc -v -x 192.168.43.240:1080 %h %p

```

http代理, 修改配置文件：`~/.gitconfig`

```bash

git config --global http.proxy http://127.0.0.1:1080

git config --global https.proxy http://127.0.0.1:1080

git config --global --unset http.proxy

git config --global --unset https.proxy

```

## 7.删除配置

```bash

git config --global --unset http.proxy
```

## 8.GPG配置


## 9.别名

```bash

git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```
