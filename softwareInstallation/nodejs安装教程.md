# nodejs安装教程

## 1.官网

[官网](https://nodejs.org/en/download/package-manager)

## 2.nvm安装

[资料](https://github.com/nvm-sh/nvm)

```bash

#
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

#
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

#
nvm install 20
```