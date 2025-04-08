# c++基础-Lambda表达式

Lambda 表达式是 C++11 引入的一种**匿名函数**写法，常用于临时、简洁地定义函数逻辑，尤其适用于 STL 算法、回调函数等场景。

---

## 1. Lambda 基础语法

```cpp
[capture](parameters) -> return_type {
    // function body
}
```

### 例子：
```cpp
auto add = [](int a, int b) -> int {
    return a + b;
};
std::cout << add(2, 3);  // 输出 5
```

### 各部分说明：

| 部分          | 说明                            |
|---------------|---------------------------------|
| `[capture]`   | 捕获外部变量（闭包）            |
| `(params)`    | 参数列表（如普通函数）          |
| `-> type`     | 返回值类型（可省略）            |
| `{...}`       | 函数体                          |

---

## 2. 省略写法（最常用）

```cpp
[] (int x) { return x * x; }
```

如果编译器能推断返回值类型，可以省略 `->`：

```cpp
auto square = [](int x) { return x * x; };
std::cout << square(4);  // 输出 16
```

---

## 3. 捕获列表详解（闭包）

### 捕获方式：

| 捕获方式 | 含义                         |
|----------|------------------------------|
| `[]`     | 不捕获任何外部变量           |
| `[=]`    | 按值捕获所有外部变量         |
| `[&]`    | 按引用捕获所有外部变量       |
| `[x]`    | 按值捕获 `x`                 |
| `[&x]`   | 按引用捕获 `x`               |
| `[=, &y]`| 按值捕获所有变量，y 按引用   |

### 示例：

```cpp
int a = 10, b = 5;
auto f1 = [=]() { return a + b; };  // 捕获 a、b 的副本
auto f2 = [&]() { a++; b++; };      // 修改外部变量（捕引用）

f2();  // a 和 b 都被修改
```

---

## 4. Lambda 与 STL 算法结合

### 排序示例：

```cpp
std::vector<int> v = {5, 2, 9, 1};

std::sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;  // 降序
});
```

### 过滤：

```cpp
std::vector<int> nums = {1, 2, 3, 4, 5};
nums.erase(
    std::remove_if(nums.begin(), nums.end(), [](int x) { return x % 2 == 0; }),
    nums.end()
);
```

---

## 5. Lambda 捕获的变量生命周期

- 按**值捕获**的变量会被拷贝进闭包，不受外部变量生命周期影响。
- 按**引用捕获**必须保证引用变量在 Lambda 执行时依然有效。

---

## 6. 可变 Lambda（mutable）

按值捕获时默认不能修改外部变量，但可以加 `mutable`：

```cpp
int a = 10;
auto f = [a]() mutable {
    a++;
    std::cout << a;  // 输出 11，但外部 a 不变
};
```

---

## 7. Lambda 类型

Lambda 是编译器生成的匿名类，其本质类似于：

```cpp
struct __Lambda {
    int operator()(int x, int y) const { return x + y; }
};
```

> 所以你可以把 `auto f = [](...) { ... }` 看作创建一个重载了 `()` 的临时类对象。

---

## 8. C++14 起的增强

### 自动类型参数（泛型 Lambda）：

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
add(1, 2);       // int
add(1.5, 2.0);   // double
```

### 初始化捕获（C++14 起）

```cpp
auto f = [val = 42]() {
    std::cout << val;
};
```

