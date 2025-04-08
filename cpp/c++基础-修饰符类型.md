# c++基础-修饰符类型


## 1. 修饰符（Type Modifiers）

> 修饰符是用来“修饰”基本数据类型或成员的关键字，用于**改变变量的大小、范围、存储特性或访问特性**。

---

## 2. 基本数据类型修饰符

### 2.1 `signed` 与 `unsigned`

用于控制变量的取值范围（是否有符号）：

| 类型            | 取值范围（假设4字节int）         |
|-----------------|----------------------------------|
| `int` 或 `signed int` | -2³¹ 到 2³¹ - 1                   |
| `unsigned int`  | 0 到 2³² - 1                      |

```cpp
unsigned int u = 4294967295;  // 最大正数
int s = -1;
```

- 注意：对 `unsigned` 的减法操作可能出现“绕回”现象。

---

### 2.2 `short` 和 `long`（数据长度）

用于扩大或缩小整数的存储位数：

| 类型        | 通常字节数（平台相关） |
|-------------|------------------|
| `short int` | 2 字节（16 位）     |
| `int`       | 4 字节（32 位）     |
| `long int`  | 4 或 8 字节         |
| `long long` | 通常为 8 字节       |

```cpp
short s = 1000;
long l = 123456789;
```

---

## 3. 常量修饰符：`const`

表示只读变量，值**不可修改**。

```cpp
const int x = 10;
x = 20;  // 错误：常量不可赋值
```

### `const` 在函数参数中常见用法：

```cpp
void print(const string& msg);  // 引用但不允许改动内容
```

### `const` 成员函数：

```cpp
class A {
public:
    void show() const;  // 表示该成员函数不会修改成员变量
};
```

---

## 4. `volatile`

告诉编译器：**该变量可能被外部程序（如硬件/中断）修改，别优化它！**

```cpp
volatile int status;
while (status == 0);  // 不要优化这段循环
```

### 用途：
- 多线程共享变量
- IO 寄存器 / 中断变量

---

## 5. `mutable`（仅用于类成员）

> 允许 `const` 成员函数**修改某些成员变量**。

```cpp
class Logger {
    mutable int count = 0;
public:
    void log() const {
        ++count;  // 合法，因为 count 是 mutable
    }
};
```

常用于缓存、计数器等。

---
