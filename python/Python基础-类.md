# Python基础-类

## 1. 类

> 类是面向对象编程（OOP）的核心，用于描述具有相同属性和方法的对象集合。

类 → 模板  
对象 → 类的实例

---

## 2. 创建类的基本语法

```python
class Person:
    def __init__(self, name, age):  # 构造方法
        self.name = name
        self.age = age

    def greet(self):  # 实例方法
        print(f"Hello, I am {self.name} and I am {self.age} years old.")

# 创建对象
p = Person("Alice", 30)
p.greet()  # Hello, I am Alice and I am 30 years old.
```

---

## 3. 类中的基本概念

### 3.1 `self` 代表实例本身

- 必须作为实例方法的第一个参数
- 用于访问和修改实例属性

### 3.2 `__init__()` 构造方法

- 创建对象时自动调用
- 初始化对象的属性

### 3.3 实例属性与方法

```python
p.name = "Bob"  # 修改属性
p.greet()       # 调用方法
```

---

## 4. 类属性 和 实例属性

```python
class Dog:
    species = "Canine"  # 类属性（共享）

    def __init__(self, name):
        self.name = name  # 实例属性（独立）

dog1 = Dog("Tom")
dog2 = Dog("Jerry")

print(dog1.species)  # Canine
print(dog1.name)     # Tom
```

---

## 5. 类方法 & 静态方法

### 5.1 类方法：用 `@classmethod` 装饰，参数是 `cls`

- 定义: 类方法是绑定到类而不是类的实例的方法。它们可以访问类的属性和方法，但不能直接访问特定实例的属性。
- 装饰器: 类方法通过 @classmethod 装饰器来定义。
- cls 参数: 类方法的第一个参数通常被命名为 cls（虽然你可以用任何名字，但 cls 是约定俗成的），它代表的是类本身，而不是类的实例。

```python
class Cat:
    count = 0

    def __init__(self):
        Cat.count += 1

    @classmethod
    def get_count(cls):
        return cls.count

print(Cat.get_count())
```

### 5.2 静态方法：用 `@staticmethod` 装饰，无 `self/cls`

- 定义: 静态方法是绑定到类但不依赖于类的实例或类本身的方法。它们本质上是定义在类中的普通函数，只是因为逻辑上与该类相关而放在一起。
- 装饰器: 静态方法通过 @staticmethod 装饰器来定义。
- 没有特殊的参数: 静态方法没有像实例方法（self）或类方法（cls）那样的特殊的第一参数。

```python
class Math:
    @staticmethod
    def add(x, y):
        return x + y

print(Math.add(3, 4))  # 7
```

---

## 6. 继承

```python
class Animal:
    def speak(self):
        print("Animal speaks")

class Dog(Animal):
    def speak(self):
        print("Dog barks")

d = Dog()
d.speak()  # Dog barks
```

- 子类可以重写父类方法
- 使用 `super()` 调用父类方法

```python
class Dog(Animal):
    def speak(self):
        super().speak()
        print("Dog also barks")
```

---

## 7. 特殊方法（魔法方法）

[官方文档](https://docs.python.org/3.13/reference/datamodel.html#special-method-names)


按功能分类：

**对象创建与初始化:**

* `__new__(cls, *args, **kwargs)`: 创建对象实例。
* `__init__(self, *args, **kwargs)`: 初始化对象属性。
* `__del__(self)`: 对象即将被销毁时调用。

**字符串表示:**

* `__str__(self)`: 返回对象的“非正式”字符串表示，用于 `str()` 和 `print()`。
* `__repr__(self)`: 返回对象的“正式”字符串表示，用于 `repr()` 和交互式环境。

**比较操作:**

* `__lt__(self, other)`: 小于 (`<`)
* `__le__(self, other)`: 小于等于 (`<=`)
* `__eq__(self, other)`: 等于 (`==`)
* `__ne__(self, other)`: 不等于 (`!=`)
* `__gt__(self, other)`: 大于 (`>`)
* `__ge__(self, other)`: 大于等于 (`>=`)

**数值运算:**

* `__add__(self, other)`: 加法 (`+`)
* `__sub__(self, other)`: 减法 (`-`)
* `__mul__(self, other)`: 乘法 (`*`)
* `__truediv__(self, other)`: 真除法 (`/`)
* `__floordiv__(self, other)`: 地板除法 (`//`)
* `__mod__(self, other)`: 取模 (`%`)
* `__pow__(self, other)`: 幂运算 (`**`)
* 以及反向运算 (`__radd__`, `__rsub__` 等) 和原地运算 (`__iadd__`, `__isub__` 等)。

**容器类型操作:**

* `__len__(self)`: 返回容器的长度 (`len()`).
* `__getitem__(self, key)`: 获取指定键的元素 (`[]`).
* `__setitem__(self, key, value)`: 设置指定键的元素 (`[] =`).
* `__delitem__(self, key)`: 删除指定键的元素 (`del`).
* `__iter__(self)`: 返回对象的迭代器。
* `__next__(self)`: 迭代器返回下一个元素。
* `__contains__(self, item)`: 检查容器是否包含指定元素 (`in`).

**可调用对象:**

* `__call__(self, *args, **kwargs)`: 使对象可以像函数一样被调用。

**上下文管理器:**

* `__enter__(self)`: 进入 `with` 语句块时调用。
* `__exit__(self, exc_type, exc_val, exc_tb)`: 退出 `with` 语句块时调用。

**属性访问控制:**

* `__getattr__(self, name)`: 访问不存在的属性时调用。
* `__setattr__(self, name, value)`: 设置属性值时调用。
* `__delattr__(self, name)`: 删除属性时调用。

**类型转换:**

* `__int__(self)`: 转换为整数 (`int()`).
* `__float__(self)`: 转换为浮点数 (`float()`).
* `__bool__(self)`: 转换为布尔值 (`bool()`).

**哈希和相等性:**

* `__hash__(self)`: 计算对象的哈希值 (`hash()`).


```python
class Book:
    def __init__(self, title):
        self.title = title

    def __str__(self):
        return f"Book: {self.title}"

b = Book("Python")
print(b)  # Book: Python
```

---

## 8. 封装（Encapsulation）

### 8.1 公有 / 受保护 / 私有属性

```python
class Demo:
    def __init__(self):
        self.a = "public"
        self._b = "protected"
        self.__c = "private"
```

### 8.2 访问私有属性

```python
obj = Demo()
print(obj._Demo__c)  # 私有属性需用 _类名__属性名 访问
```

---

## 9. 属性装饰器 `@property`

让方法像属性一样使用：

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def area(self):
        return 3.14 * self._radius ** 2

c = Circle(5)
print(c.area)  # 78.5
```


---

## 10. 类的高级特性

### 10.1 多继承

```python
class A: pass
class B: pass
class C(A, B): pass
```

Python 会使用 **MRO（方法解析顺序）** 查找方法。

方法调用搜索顺序：C-->A-->B，找到后执行

### 10.2 动态添加属性/方法

```python
class Human:
    pass

h = Human()
h.name = "Tom"
print(h.name)  # Tom
```

---


