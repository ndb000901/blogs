# c++基础-函数重载


## 1. 函数重载

**函数重载**：在同一个作用域中，允许定义多个**函数名相同**但**参数列表不同**的函数，编译器根据实参自动选择匹配的版本。

```cpp
void print(int i);
void print(double d);
void print(const std::string& s);
```

---

## 2. 重载的条件

函数名相同，但**参数列表不同**（满足以下任一）：

| 条件 | 举例 |
|------|------|
| 参数个数不同 | `print(int)`, `print(int, int)` |
| 参数类型不同 | `print(int)`, `print(double)` |
| 参数顺序不同 | `print(int, double)`, `print(double, int)` |

**注意：不能仅通过返回值区别重载！**

```cpp
int func(int);     // OK
double func(int);  // 错误：只返回值不同
```

---

## 3. 重载解析过程（编译器做了什么）

编译器在看到调用时，会：

1. 找出所有同名函数；
2. 筛选参数数量匹配的；
3. 进行**类型匹配**（完全匹配优先）；
4. 如果存在多个“模糊匹配”，编译报错。

---

## 4. 示例

```cpp
void show(int x)      { std::cout << "int\n"; }
void show(double x)   { std::cout << "double\n"; }
void show(float x)    { std::cout << "float\n"; }

int main() {
    show(10);         // 输出：int
    show(3.14);       // 输出：double
    show(3.14f);      // 输出：float
}
```

---

## 5. 函数重载的注意事项

### 5.1 默认参数与重载的冲突

```cpp
void func(int x, int y = 10);
void func(int x);     // 冲突：调用 func(5) 会二义性
```

### 5.2 与函数模板结合

```cpp
void print(int x) {
    std::cout << "normal\n";
}

template<typename T>
void print(T x) {
    std::cout << "template\n";
}

print(42); // 优先调用普通函数
print("hi"); // 模板匹配
```

### 5.3 命名空间和重载

同一命名空间下的函数可以重载，跨命名空间则视为不同函数。

---

## 6. 重载 vs 重写（override） vs 隐藏（hide）

| 特性 | 关键字 | 范围 | 是否运行时绑定 |
|------|--------|------|----------------|
| 重载 | 无     | 同一作用域 | 否（编译期） |
| 重写 | `override` | 基类/派生类 | 是（运行期） |
| 隐藏 | 无（非虚） | 基类函数被派生类同名函数挡住 | 否（编译期） |

**示例：**

```cpp
class Base {
public:
    void func(int);
};

class Derived : public Base {
public:
    void func(double); // 隐藏 Base::func(int)
};
```

要解决隐藏可以显式引入：
```cpp
using Base::func;
```

---

## 7. 函数重载底层实现

编译器通过 **名称修饰（Name Mangling）** 来区分多个重载函数，例如：

```cpp
void foo(int);     // 编译后可能变成 _Z3fooi
void foo(double);  // 变成 _Z3food
```

这也是为什么 C++ 函数名不能直接被 C 调用，要加 `extern "C"` 来禁止 name mangling。

---



