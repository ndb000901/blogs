# ollama 基础使用

Ollama 是一个开源的 本地大语言模型（LLM）运行平台，可以用它在自己的电脑（macOS、Linux、Windows WSL）上非常方便地下载、管理和运行主流的大语言模型。

[官网](https://ollama.com/)

## 1. 安装

```bash

curl -fsSL https://ollama.com/install.sh | sh
```

## 2. 命令使用

### 2.1 `serve`

启动 Ollama 的后端服务（通常自动由 `run` 启动，不需手动运行）

```bash

ollama serve
```

* 启动后台服务（HTTP API 默认监听在 `localhost:11434`）。
* 通常你只在部署服务端 API 时才用到。

---

### 2.2 `create`

从 `Modelfile` 创建一个自定义模型（支持添加 System Prompt、修改基础模型等）

```bash

ollama create my-zh-bot -f ./Modelfile
```

示例 `Modelfile` 内容：

```Dockerfile
FROM llama3
SYSTEM "你是一个中文助手，所有回答都用中文。"
```

> 创建成功后你可以用 `ollama run my-zh-bot` 使用它。

---

### 2.3 `show`

显示模型的详细信息（metadata）

```bash

ollama show llama3
```

输出内容示例（包含量化方式、模型大小、系统提示、模板信息等）。

---

### 2.4 `run`

运行模型，进入交互式聊天终端。

```bash

ollama run llama3
```

> 第一次运行会自动下载模型。用 Ctrl+C 退出。

---

### 2.5 `stop`

停止一个运行中的模型或全部模型服务。

```bash

ollama stop llama3
```

或停止所有模型（后台服务）：

```bash
ollama stop
```

---

### 2.6 `pull`

从官方或私有 registry 拉取模型。

```bash

ollama pull llama3
ollama pull mistral
ollama pull orca-mini
```

---

### 2.7 `push`

将模型推送到远程注册中心（需要登录和权限）

```bash

ollama push my-zh-bot
```


---

### 2.8 `list`

列出本地安装的所有模型

```bash

ollama list
```

输出类似：

```
MODEL           SIZE      MODIFIED
llama3          3.8 GB    2024-06-01
mistral         4.2 GB    2024-06-01
```

---

### 2.9 `ps`

列出当前正在运行的模型（和运行状态）

```bash

ollama ps
```

输出类似：

```
NAME        STATUS    PORT
llama3      running   11434
```

---

### 2.10 `cp`

复制已有模型作为新模型（适合修改后再次构建）

```bash

ollama cp llama3 my-new-llama
```

然后你可以基于 `my-new-llama` 修改参数或再构建。

---

### 2.11 `rm`

删除本地模型（释放空间）

```bash

ollama rm llama3
```

---

