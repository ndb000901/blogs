# Python基础-包

## 1. 包（Package）

> 包是**多个模块的集合**，用来组织 Python 模块文件。  
它本质上是一个包含 `__init__.py` 文件的 **文件夹目录**。

---

## 2. 模块 vs 包

| 概念  | 模块（Module）           | 包（Package）                      |
|-------|---------------------------|------------------------------------|
| 本质  | 一个 `.py` 文件           | 一个包含 `__init__.py` 的文件夹   |
| 用途  | 封装一组功能              | 管理多个模块/子包                 |
| 例子  | `math.py`, `os.py`       | `numpy`, `requests`, `my_package` |

---

## 3. 包的结构示例

```bash

my_package/
├── __init__.py
├── module1.py
├── module2.py
└── subpackage/
    ├── __init__.py
    └── submodule.py
```

- `__init__.py`：标识这是一个包（可为空，也可做初始化）

---



## 4. 相对导入和绝对导入

### 绝对导入（推荐）

```python
from my_package.module1 import hello
```

### 相对导入（仅在包内部用）

```python
from .module1 import hello  # 当前目录
from ..subpackage import submodule  # 父目录
```

注意：相对导入只能用于**包内模块调用**，不能用于顶层脚本执行。

---

