# Python基础-函数

## 1. 函数

函数是组织好的、可重复使用的代码，用来实现特定功能。使用函数可以让程序结构更清晰、易维护、可复用。

---

## 2. 函数的基本语法

```python
def 函数名(参数1, 参数2, ...):
    """文档字符串，可选"""
    函数体
    return 返回值（可选）
```

### 示例：
```python
def greet(name):
    print(f"Hello, {name}!")
```

---

## 3. 函数参数类型

### 必选参数
最普通的参数，必须按顺序传递。
```python
def add(a, b):
    return a + b

add(1, 2)
```

### 默认参数
参数有默认值，调用时可不传。
```python
def greet(name="Guest"):
    print(f"Hello, {name}!")
```

### 可变参数（*args）
可以传任意多个参数，函数内部是一个元组。
```python
def sum_all(*args):
    return sum(args)

sum_all(1, 2, 3, 4)  # 结果为 10
```

### 关键字参数（**kwargs）
用于接收多个关键字参数，函数内部是一个字典。
```python
def show_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

show_info(name="Tom", age=18)
```

### 组合使用顺序：
```python
def func(a, b=2, *args, **kwargs):
    pass
```

---

## 4. 返回值 return

函数中可以使用 `return` 返回值，多个返回值是以元组形式返回。

```python
def get_point():
    return 3, 4  # 实际返回 (3, 4)

x, y = get_point()
```

---

## 5. 文档字符串（Docstring）

用于给函数添加说明文档，写在函数定义下第一行，用三引号。

```python
def add(a, b):
    """
    这是一个加法函数
    :param a: 第一个数
    :param b: 第二个数
    :return: a + b 的结果
    """
    return a + b

print(add.__doc__)
```

---

## 6. 匿名函数（lambda）

lambda 表达式用于定义简短的小函数，语法：

```python
lambda 参数: 表达式
```

### 示例：
```python
add = lambda a, b: a + b
print(add(3, 5))  # 输出 8
```

常用于 `sorted()`、`map()`、`filter()` 等函数中作为回调。

---

## 7. 函数的嵌套与闭包

### 函数嵌套
```python
def outer():
    def inner():
        print("我是内部函数")
    inner()
```

### 闭包（Closure）
内部函数引用了外部函数的变量。
```python
def outer(x):
    def inner():
        print(x)
    return inner

f = outer(10)
f()  # 输出 10
```

---

## 8. 高阶函数

函数作为参数传入另一个函数，或者返回函数。

```python
def operate(f, a, b):
    return f(a, b)

print(operate(lambda x, y: x + y, 3, 5))  # 输出 8
```

---

## 9. 装饰器（Decorator）

本质是一个函数，用来扩展其他函数的功能。

```python
def log(func):
    def wrapper(*args, **kwargs):
        print(f"调用 {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log
def greet(name):
    print(f"Hi, {name}")

greet("Alice")
```

---