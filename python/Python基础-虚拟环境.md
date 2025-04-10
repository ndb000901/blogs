# Python基础-虚拟环境

## 1. 虚拟环境

> 虚拟环境是一种**隔离的 Python 运行环境**，用于管理**项目依赖的包和 Python 版本**，不会影响系统的 Python 环境。

- 多项目隔离：不同项目可能依赖不同版本的库
- 防止包冲突：避免一个项目升级依赖影响其他项目
- 不污染全局：不在全局 Python 中安装包

---

## 2. 常见虚拟环境工具

| 工具名        | 说明                                 |
|---------------|--------------------------------------|
| `venv`        | Python 标准库提供，推荐使用         |
| `virtualenv`  | 第三方工具，兼容性更强               |
| `conda`       | Anaconda 提供，适用于科学计算         |
| `pipenv`      | 自动管理 `venv` 和 `requirements`    |
| `poetry`      | 现代项目管理工具，集成依赖管理与打包 |

---

## 3. `venv` 创建与使用

### 3.1 创建虚拟环境

```bash

python3 -m venv myenv
```

说明：
- `myenv`：虚拟环境文件夹名，可以随便取

### 3.2 激活虚拟环境

| 系统     | 命令                                 |
|----------|--------------------------------------|
| Windows  | `myenv\Scripts\activate.bat`         |
| Linux/macOS | `source myenv/bin/activate`       |

激活后会看到命令行前面多了 `(myenv)`。

### 3.3 安装依赖

```bash

pip install requests flask
```

这些依赖只会安装在 `myenv` 中，对系统环境无影响。

### 3.4 查看已安装的依赖

```bash

pip list
```

### 3.5 导出依赖列表

```bash

pip freeze > requirements.txt
```

用于记录项目依赖。

### 3.6 从依赖列表安装

```bash

pip install -r requirements.txt
```

常用于部署项目。

### 3.7 退出虚拟环境

```bash

deactivate
```

---


