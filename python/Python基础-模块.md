# Python基础-模块


## 1. 模块

> 模块（module）就是一个 **Python 文件（.py）**，内部可以包含变量、函数、类、甚至其他模块。


- 提高代码复用性
- 更好的组织结构（分模块开发）
- 避免命名冲突（使用命名空间）
- 实现功能分层（逻辑清晰）

---

## 2. 模块的使用方式

### 1. `import` 导入整个模块

```python
import math
print(math.sqrt(16))  # 4.0
```

### 2. `from ... import ...` 导入模块中的某个部分

```python
from math import pi
print(pi)  # 3.141592653589793
```

### 3. `as` 起别名

```python
import math as m
print(m.sin(m.pi / 2))  # 1.0
```

---

## 3. 自定义模块

你可以创建一个 `.py` 文件作为模块，例如 `mymodule.py`：

```python
# mymodule.py
def hello():
    print("Hello from mymodule")
```

使用：

```python
import mymodule
mymodule.hello()
```

或者：

```python
from mymodule import hello
hello()
```

---

## 4. 模块的搜索路径（`sys.path`）

Python 会按顺序搜索模块的位置：

```python
import sys
print(sys.path)
```

搜索顺序：

1. 当前目录
2. 环境变量 `PYTHONPATH` 中指定的目录
3. 安装的标准库和第三方库路径

---

## 5.模块与命名空间

每个模块都是一个独立的命名空间，导入时不会污染当前作用域：

```python
# mymath.py
x = 100

# main.py
import mymath
print(mymath.x)  # 使用模块前缀访问
```

---

## 6. 模块的执行机制

### `__name__ == "__main__"` 的意义：

```python
# mod.py
def func():
    print("func")

if __name__ == "__main__":
    func()
```

- **直接执行该文件**时：`__name__ == "__main__"`，会运行 `func()`
- **被 import 导入**时：不会执行 `func()`

> 用来实现模块既能被导入，也能独立运行做测试！

---

## 7. 包（Package）和模块的区别

- **模块（module）**：就是 `.py` 文件
- **包（package）**：是一个目录，里面有 `__init__.py`（可为空），可以包含多个模块或子包

### 示例结构：

```
mypkg/
├── __init__.py
├── utils.py
└── math_tools.py
```

使用：

```python
from mypkg import utils
from mypkg.math_tools import add
```

---

## 8. 添加库扫描路径

将项目lib目录假如扫描路径

```python

# src/hello.py
import sys
import os

# 获取当前文件 (hello.py) 的所在目录 (src)
current_dir = os.path.dirname(os.path.abspath(__file__))
# 获取 src 目录的父目录 (项目根目录)
parent_dir = os.path.dirname(current_dir)
# 构建 lib 目录的完整路径
lib_dir = os.path.join(parent_dir, 'lib')

# 将 lib 目录添加到 sys.path
sys.path.append(lib_dir)

# 现在你可以导入 mymath 包了
from mymath import math

# 使用 mymath 包中的模块
result = math.add(5, 3)
print(f"5 + 3 = {result}")
```


## 9. 其他

- 使用 `__all__` 控制 `from module import *` 时可导出的内容
- 使用 `reload(module)` 动态重载模块（需要 `import importlib`）
- 使用虚拟环境（`venv`）来隔离第三方模块

