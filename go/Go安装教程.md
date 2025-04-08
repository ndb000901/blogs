# Go 安装

## 1. 下载

[下载页面](https://go.dev/dl/)

```bash

wget -c https://go.dev/dl/go1.24.2.linux-amd64.tar.gz
```

## 2. 解压

```bash

tar -zxvf go1.24.2.linux-amd64.tar.gz
```

## 3. 设置环境变量

```bash

# vim ~/.zshrc
export GOROOT=/home/hello/local/go
export PATH=$PATH:$GOROOT/bin

# 刷新
source ~/.zshrc
```

## 4.验证

```bash

go version
```

## 5.vscode 配置

Settings->Goroot 配置

```json

{
    "settings": {
        "C_Cpp.intelliSenseEngine": "default"
    },
    "C_Cpp.default.browse.databaseFilename": "default",
    "C_Cpp.intelliSenseEngine": "default",
    "C_Cpp.default.intelliSenseMode": "linux-gcc-x64",
    "C_Cpp.default.compilerPath": "/usr/bin/gcc",
    "go.goroot": "/home/hello/local/go"
}

```

### 开发依赖

```bash

go install golang.org/x/tools/gopls@latest
go install github.com/go-delve/delve/cmd/dlv@latest
go install golang.org/x/tools/cmd/goimports@latest
go install golang.org/x/lint/golint@latest
go install honnef.co/go/tools/cmd/staticcheck@latest
go install github.com/ramya-rao-a/go-outline@latest
go install github.com/uudashr/gopkgs/v2/cmd/gopkgs@latest
go install github.com/acroca/go-symbols@latest
go install github.com/cweill/gotests/gotests@latest
go install github.com/fatih/gomodifytags@latest
go install github.com/josharian/impl@latest
go install github.com/rogpeppe/godef@latest
```

---

### 工具说明（按功能分类）

| 工具名 | 功能描述 |
|--------|----------|
| `gopls` | 核心语言服务器，提供补全、跳转、重构等 |
| `dlv` | Delve 调试器，Go 官方调试工具 |
| `goimports` | 自动导入包并格式化代码 |
| `golint` | Go 代码静态检查（风格级别） |
| `staticcheck` | 高级静态分析工具 |
| `go-outline` | 生成符号大纲视图（VSCode 左侧结构） |
| `gopkgs` | 列出可导入的包，用于自动补全 |
| `go-symbols` | 查找文件/函数符号，支持 Ctrl+T 快捷键 |
| `gotests` | 自动生成测试代码 |
| `gomodifytags` | 自动为 struct 添加/修改 JSON 等 tag |
| `impl` | 根据接口生成结构体方法实现 |
| `godef` | 跳转到定义（备用） |

