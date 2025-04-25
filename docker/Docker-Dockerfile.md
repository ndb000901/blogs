# Docker Dockerfile


`Dockerfile` 是一个包含一组指令的文本文件，用于告诉 Docker **如何构建一个镜像**。这些指令定义了：

- 使用哪个基础镜像
- 安装哪些依赖
- 拷贝哪些文件
- 设置哪些环境变量
- 容器启动时运行什么命令等

---

## 1. Dockerfile 指令（按执行顺序）

| 指令 | 说明 |
|------|------|
| `FROM`       | 指定基础镜像（必须） |
| `LABEL`      | 添加元数据（如作者、版本） |
| `RUN`        | 构建时执行命令（安装依赖等） |
| `COPY` / `ADD` | 拷贝文件到镜像中 |
| `WORKDIR`    | 设置工作目录 |
| `ENV`        | 设置环境变量 |
| `EXPOSE`     | 声明容器暴露的端口（仅文档用途） |
| `VOLUME`     | 声明挂载点 |
| `CMD`        | 容器启动时默认执行的命令（可被覆盖） |
| `ENTRYPOINT` | 容器启动时执行的命令（不易被覆盖） |
| `USER`       | 设置运行容器时的用户 |
| `ARG`        | 构建参数，和 ENV 类似但仅构建时有效 |
| `HEALTHCHECK`| 设置容器健康检查命令 |
| `ONBUILD`    | 父镜像中触发子镜像行为（不常用） |
| `SHELL`      | 更改 RUN/ENTRYPOINT/CMD 的 shell |

---

## 2. 实用案例：Node.js 应用

```dockerfile
# 1. 基础镜像
FROM node:18-alpine

# 2. 作者信息
LABEL maintainer="yourname@example.com"

# 3. 设置工作目录
WORKDIR /app

# 4. 复制 package.json 并安装依赖
COPY package*.json ./
RUN npm install

# 5. 拷贝源代码
COPY . .

# 6. 设置环境变量
ENV NODE_ENV=production

# 7. 暴露端口
EXPOSE 3000

# 8. 默认命令
CMD ["node", "index.js"]
```

---

## 3. 构建和运行

```bash

docker build -t myapp .
docker run -p 3000:3000 myapp
```

---

## 4. CMD vs ENTRYPOINT 区别

| 特性 | CMD | ENTRYPOINT |
|------|---|----------|
| 是否能被 `docker run` 参数覆盖 | 是 | 否 |
| 是否适合当作容器主进程 | 不推荐 | 推荐 |
| 组合方式 | 常配合 ENTRYPOINT 一起用 | 通常用于主命令固定的场景 |

示例（组合用法）：

```dockerfile
ENTRYPOINT ["python3", "app.py"]
CMD ["--port", "80"]
```

最终执行命令：`python3 app.py --port 80`

---

## 5. COPY vs ADD 区别

| 指令 | 功能 |
|------|------|
| `COPY` | 仅复制文件（推荐） |
| `ADD` | 支持 URL、自动解压 `.tar`，功能更多但不透明 |

通常推荐用 `COPY`，保持简单可控。

---

