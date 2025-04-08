# c++基础-泛型

## 1. 泛型

**泛型（Generic Programming）** 是一种**编程范式**，编写与数据类型无关的代码，使得代码可以重用不同的数据类型。

在 C++ 中，泛型主要通过 **模板（template）机制** 来实现，因此你可以把“泛型编程 = 使用模板进行编程”。

---

## 2. 泛型好处

想象你要写两个函数，一个处理 `int`，一个处理 `double`：

```cpp
int max(int a, int b) { return a > b ? a : b; }
double max(double a, double b) { return a > b ? a : b; }
```

你会发现：**逻辑是一样的，仅数据类型不同**。

这时就可以用模板封装成**泛型函数**：

```cpp
template <typename T>
T max(T a, T b) {
    return a > b ? a : b;
}
```

---

## 3. C++ 泛型编程的主要工具：模板

### 函数模板

```cpp
template <typename T>
T square(T x) {
    return x * x;
}
```

调用：

```cpp
square<int>(5);   // 显式指定
square(5.5);      // 类型自动推导
```

### 类模板

```cpp
template <typename T>
class Box {
public:
    T value;
    void set(T v) { value = v; }
    T get() { return value; }
};
```

调用：

```cpp
Box<int> b1;
b1.set(42);

Box<std::string> b2;
b2.set("hello");
```

