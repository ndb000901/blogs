# c++基础-联合


联合是一种特殊的用户自定义类型，**多个成员共享同一块内存区域**，一次只能保存其中一个成员的值。



## 1. 语法格式

```cpp
union 联合名 {
    类型1 成员1;
    类型2 成员2;
    ...
};
```

### 示例：

```cpp
union Data {
    int i;
    float f;
    char str[20];
};
```

---

## 2. 使用方式

```cpp
#include <iostream>
using namespace std;

union Data {
    int i;
    float f;
    char str[20];
};

int main() {
    Data data;
    data.i = 10;
    cout << "data.i = " << data.i << endl;

    data.f = 3.14;
    cout << "data.f = " << data.f << endl;
    cout << "data.i = " << data.i << endl; // 被覆盖
}
```

### 输出结果：
```
data.i = 10
data.f = 3.14
data.i = 1078523331 // 已被覆盖（由于共用内存）
```

---

## 3. 联合 vs 结构体

| 比较项       | `struct`                             | `union`                      |
|--------------|--------------------------------------|------------------------------|
| 内存占用     | 所有成员大小之和(也许大于成员之和，会有内存对齐，使用sizeof计算) | 最大成员大小                 |
| 数据存储     | 成员独立存在                               | 成员共享一块内存             |
| 同时访问     | 可以同时使用多个成员                           | 最好只使用一个               |
| 应用场景     | 数据打包                                 | 节省空间、类型复用           |

---

## 4. 内存布局

对于：
```cpp
union U {
    int i;       // 4 字节
    double d;    // 8 字节
    char c;      // 1 字节
};
```

> 联合的大小是最大成员的大小，通常也要对齐，所以 `sizeof(U)` == 8。

---

## 5. 匿名联合（C++ 支持）

匿名联合没有名字，直接放在类中或全局作用域中，成员可直接访问。

```cpp
#include <iostream>
using namespace std;

int main() {
    union {
        int i;
        float f;
    };

    i = 42;
    cout << i << endl;
    f = 3.14;
    cout << f << endl;
}
```


