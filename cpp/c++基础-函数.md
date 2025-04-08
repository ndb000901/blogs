# c++基础-函数

## 1. 函数基础语法

```cpp
返回类型 函数名(参数列表) {
    // 函数体
    return 返回值;
}
```

### 示例：
```cpp
int add(int a, int b) {
    return a + b;
}
```

---

## 2. 参数传递方式

| 方式            | 说明                            | 示例                          |
|------------------|----------------------------------|-------------------------------|
| 值传递（默认）     | 传入副本，函数内部修改不影响外部 | `void foo(int x)`             |
| 指针传递          | 可修改实参                       | `void foo(int* x)`            |
| 引用传递          | 可修改实参，效率高                | `void foo(int& x)`            |
| 常量引用传递       | 只读引用，避免拷贝开销            | `void foo(const std::string& s)` |

### 示例：
```cpp
void update(int& a) { a += 1; }
```

---

## 3. 函数重载（Overloading）

- 同名函数，参数列表不同
- 与返回值无关

```cpp
int add(int a, int b);
double add(double a, double b);
```

---

## 4. 默认参数

```cpp
void greet(std::string name = "Guest") {
    std::cout << "Hello, " << name << std::endl;
}
```

---

## 5. 内联函数 `inline`

- 编译器将函数调用展开为函数体，**减少函数调用开销**
- 适合短小频繁调用的函数
- 定义在头文件中

```cpp
inline int square(int x) {
    return x * x;
}
```

编译器可能**不一定采纳 inline 建议**。

---

## 6. 函数指针

### 定义函数指针
```cpp
int add(int a, int b) {
    return a + b;
}

int (*func_ptr)(int, int) = add;
```

### 使用
```cpp
int result = func_ptr(3, 4); // 调用
```

---

## 7. Lambda 表达式（C++11）

- 匿名函数，可捕获变量

```cpp
auto f = [](int a, int b) {
    return a + b;
};
int result = f(3, 4);
```

### 捕获示例
```cpp
int x = 10;
auto show = [x]() { std::cout << x; };
```

| 捕获方式 | 说明                     |
|----------|--------------------------|
| `[=]`    | 按值捕获全部变量         |
| `[&]`    | 按引用捕获全部变量       |
| `[x]`    | 只按值捕获变量 x         |
| `[&x]`   | 只按引用捕获变量 x       |

---

## 8. 模板函数（Function Template）

- 写通用函数，自动推导类型

```cpp
template <typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}
```

使用：
```cpp
int a = max(3, 5);
double b = max(3.5, 2.1);
```

---

## 9. 递归函数

- 函数调用自身，需**终止条件**

```cpp
int factorial(int n) {
    if (n == 1) return 1;
    return n * factorial(n - 1);
}
```

---

## 10. 函数返回类型

| 返回类型     | 说明                           |
|--------------|--------------------------------|
| `int`、`double`等 | 基本类型                     |
| `void`        | 无返回值                      |
| `T&`          | 返回引用，避免拷贝，注意生存期 |
| `const T&`    | 返回常量引用，避免拷贝         |
| `T*`          | 返回指针，需注意释放           |
| `auto`        | 自动推导返回值（C++14起推荐） |

---

