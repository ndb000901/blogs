# Python基础-装饰器


## 1. 装饰器

**装饰器是一种用于扩展函数功能的语法糖。**

本质上：装饰器是一个**高阶函数**，它**接收一个函数作为参数**，并返回一个新的函数。

---

## 2.简单例子

```python
def my_decorator(func):
    def wrapper():
        print("开始执行")
        func()
        print("执行结束")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```

输出：

```
开始执行
Hello!
执行结束
```

> 使用 `@my_decorator` 相当于：`say_hello = my_decorator(say_hello)`

---

## 3. 装饰器函数结构解析

```python
def decorator(func):          # 接收函数作为参数
    def wrapper(*args, **kwargs):  # 支持传入任意参数
        # 添加功能
        return func(*args, **kwargs)  # 调用原函数
    return wrapper  # 返回新函数
```

---

## 4. 支持参数的装饰器

```python
def log_args(func):
    def wrapper(*args, **kwargs):
        print(f"调用参数：args={args}, kwargs={kwargs}")
        return func(*args, **kwargs)
    return wrapper

@log_args
def add(a, b):
    return a + b

print(add(3, 5))
```

输出：

```
调用参数：args=(3, 5), kwargs={}
8
```

---

## 5. 保留原函数元信息（推荐使用 `functools.wraps`）

```python
import functools

def my_decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

> 如果不使用 `functools.wraps`，被装饰的函数 `__name__`、`__doc__` 等元信息会丢失。

---

## 6. 装饰器链式叠加

```python
def d1(func):
    def wrapper():
        print("d1")
        func()
    return wrapper

def d2(func):
    def wrapper():
        print("d2")
        func()
    return wrapper

@d1
@d2
def hello():
    print("hello")

hello()
```

输出：

```
d1
d2
hello
```

> 注意：**执行顺序是自下而上、内层先执行。**

---




