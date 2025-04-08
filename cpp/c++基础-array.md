# c++基础-array


## 1. `std::array`

`std::array` 是一个**模板类**，用来封装静态数组（即大小在编译期确定），并提供了 STL 风格的接口，比如 `.size()`、`.begin()`、`.end()`、`.fill()` 等。

```cpp
#include <array>
std::array<int, 5> arr = {1, 2, 3, 4, 5};
```

等价于原生数组：`int arr[5] = {1, 2, 3, 4, 5};`，但功能更强！

---

## 2. 基本语法

```cpp
std::array<类型, 长度> 变量名;
```

例子：

```cpp
std::array<int, 3> a = {10, 20, 30};
```

访问方式同数组：

```cpp
std::cout << a[0];      // 10
std::cout << a.at(1);   // 安全访问，20
```

---

## 3. 示例

### 3.1 初始化

```cpp
std::array<int, 3> a1 = {1, 2, 3};
std::array<int, 3> a2 = {};           // 全为0
std::array<int, 3> a3{};              // 同上
```

---

### 3.2 访问元素

```cpp
a[0];            // 不检查越界，和普通数组一样快
a.at(1);         // 检查越界，越界时抛异常
```

---

### 3.3 遍历元素

```cpp
for (size_t i = 0; i < a.size(); ++i) std::cout << a[i];

for (int x : a) std::cout << x;

std::for_each(a.begin(), a.end(), [](int x){ std::cout << x; });
```

---

### 3.4 填充/拷贝/交换

```cpp
a.fill(42);        // 全部填充为42

std::array<int, 3> b = a;    // 拷贝
a.swap(b);                   // 交换内容
```

---

### 3.5 元信息

```cpp
a.size();          // 元素数量
a.empty();         // 是否为空（始终 false）
a.front();         // 第一个元素
a.back();          // 最后一个元素
```

---

### 3.6 支持解构

```cpp
std::array<int, 3> a = {1, 2, 3};
auto [x, y, z] = a;     // C++17 的结构化绑定
```

---

### 3.7 与函数配合传参

```cpp
void print(std::array<int, 3> a) {
    for (int x : a) std::cout << x << " ";
}
```

---

## 4. 底层原理简析

```cpp
template<typename T, std::size_t N>
struct array {
    T elems[N];          // 实际元素
    // 其他函数成员如 begin/end 等
};
```

也就是说，`std::array` **本质还是栈上数组**，但提供了 STL 容器的语法糖和安全性。

---

## 5. 注意事项

1. **大小在编译期就要确定**，不能动态改变。
2. `.at()` 有越界检查，`.operator[]` 没有。
3. 与原生数组不同，`std::array` **可以整体拷贝和赋值**。
4. 多维数组定义稍微复杂：

```cpp
std::array<std::array<int, 3>, 2> matrix = {{{1,2,3},{4,5,6}}};
```
