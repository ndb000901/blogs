# c++基础-命名空间

命名空间（`namespace`）是 C++ 提供的一种机制，用于**避免名字冲突**、**组织代码结构**，特别适用于大型项目中不同模块或库的区分。


## 1. 基本语法

```cpp
namespace MyNamespace {
    int value = 42;
    void print();
}
```

使用命名空间成员：

```cpp
MyNamespace::print();
std::cout << MyNamespace::value;
```

---

## 2. 命名空间分类与用法表

| 类型                     | 说明                                | 示例                              |
|--------------------------|-------------------------------------|-----------------------------------|
| 命名命名空间             | 显式命名的 namespace                | `namespace math {}`              |
| 匿名命名空间（内部链接） | 相当于 `static`，仅文件内可见       | `namespace {}`                   |
| 嵌套命名空间             | 命名空间中再定义命名空间            | `namespace a { namespace b {} }` |
| inline 命名空间          | 支持版本兼容，避免重复使用作用域    | `inline namespace v1 {}`         |
| 命名空间别名             | 用简名替代长命名空间                | `namespace io = mylib::io;`      |
| using 声明               | 引入某个成员到当前作用域            | `using std::cout;`               |
| using 指令               | 引入整个命名空间                    | `using namespace std;`           |

---

## 3. 示例

### 3.1 命名命名空间

```cpp
namespace Network {
    void connect();
}
Network::connect();
```

---

### 3.2 匿名命名空间（文件内私有）

```cpp
namespace {
    int internalValue = 123;  // 类似 static，全局唯一但仅文件可见
}
```

---

### 3.3 嵌套命名空间（C++17 支持简写）

```cpp
namespace outer {
    namespace inner {
        void print();
    }
}

// C++17 简化写法：
namespace outer::inner {
    void print() {}
}
```

---

### 3.4 inline 命名空间（版本控制利器）

```cpp
namespace v1 {
    void api() {}
}

inline namespace v2 {
    void api() {}
}
```

> `api()` 会默认调用 v2::api，v1::api 仍可显式调用：`v1::api()`。

---

### 3.5 命名空间别名

```cpp
namespace very::long::namespace::name {
    void func();
}

namespace short = very::long::namespace::name;
short::func();  // 简洁调用
```

---

## 4. using namespace 的风险

虽然在 **.cpp 文件中短期方便**，但：

```cpp
using namespace std;
```

不建议放在 **头文件** 或 **命名冲突易发区域**，容易引发：

- 名字冲突（如 `count`, `distance` 与用户函数冲突）
- 可读性下降
- 编译时出错变得难定位


