# Python基础-作用域


## 1. 作用域

作用域（Scope）就是**变量可访问的范围**。它决定了你在哪些位置可以引用这个变量。

---

## 2. Python 的作用域规则：LEGB 原则

Python 采用 **LEGB** 的作用域查找顺序：

| 缩写 | 全称          | 说明                                         |
|------|---------------|----------------------------------------------|
| L    | Local         | 函数内部定义的局部变量                      |
| E    | Enclosing     | 外部嵌套函数的作用域（非全局）              |
| G    | Global        | 当前模块的全局作用域                        |
| B    | Built-in      | Python内建作用域，如 `len`, `range` 等     |

当 Python 解释器查找一个变量时，会按照这个顺序逐层查找。

---

## 3. LEGB 示例

```python
x = "global"

def outer():
    x = "enclosing"
    
    def inner():
        x = "local"
        print(x)  # 输出 local

    inner()

outer()
```

如果你去掉 `x = "local"`，它会输出 `enclosing`，再去掉，就是 `global`，最后是内建。

---

## 4. 局部变量（Local Scope）

在函数或方法内部定义的变量，只能在函数内部使用。

```python
def func():
    a = 10  # a 是局部变量
    print(a)

func()
# print(a)  # 报错：NameError: name 'a' is not defined
```

---

## 5. 全局变量（Global Scope）

在函数外部定义的变量，整个模块都能访问（如果不被重新赋值）。

```python
x = 100

def show():
    print(x)  # 可以访问全局变量

show()
```

### 但如果你想在函数里**修改**全局变量，需要用 `global` 声明：

```python
x = 10

def update():
    global x
    x = 20

update()
print(x)  # 输出 20
```

---

## 6. 闭包变量（Enclosing Scope）

在**嵌套函数**中，内部函数可以访问外部函数的变量，这些变量就处于 enclosing 作用域。

```python
def outer():
    msg = "hello"

    def inner():
        print(msg)  # msg 在 enclosing 作用域

    inner()

outer()
```

---

## 7. nonlocal 关键字

如果要在嵌套函数中**修改外层函数的变量**，需要使用 `nonlocal`。

```python
def outer():
    count = 0

    def inner():
        nonlocal count
        count += 1
        print(count)

    inner()
    inner()

outer()
```

> 如果不使用 `nonlocal`，`count += 1` 会报错，因为 Python 会当成是 inner 的局部变量。

---

## 8. 内建作用域（Built-in Scope）

Python 自带的内建变量和函数，比如：

```python
print(len("hello"))  # len 是内建作用域中的函数
```

可以通过 `dir(__builtins__)` 查看所有内建函数。

---


