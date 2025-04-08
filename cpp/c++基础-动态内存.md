# c++基础-动态内存

## 1. 动态内存

**动态内存**：程序运行时从**堆（Heap）** 分配的内存，大小和生命周期在运行时决定。

与之相对的是：
- **静态内存**：编译期分配，生命周期贯穿整个程序。
- **栈内存**：函数调用时分配，退出时自动释放。

---

## 2. 分配与释放

### `new` 和 `delete`

```cpp
int* p = new int(5);  // 在堆上分配一个整数，值为5
delete p;             // 释放内存
```

### 分配数组

```cpp
int* arr = new int[10];  // 分配长度为10的int数组
delete[] arr;            // 释放数组，必须使用 delete[]
```

注意：
- `new` 返回的是指向分配区域的指针，已初始化；
- `delete` 和 `delete[]` 必须成对使用！

---

## 3. 动态对象

```cpp
class Person {
public:
    Person() { std::cout << "构造\n"; }
    ~Person() { std::cout << "析构\n"; }
};

Person* p = new Person(); // 构造函数被调用
delete p;                 // 析构函数被调用
```

若不 `delete`，对象析构函数不会被调用 → 内存泄漏！

---

## 4. 常见错误

| 错误类别              | 示例代码                          | 问题说明                         |
|-------------------|-----------------------------------|----------------------------------|
| 内存泄漏              | `int* p = new int(10);`           | 未 delete，造成堆空间无法释放   |
| 重复释放              | `delete p; delete p;`             | 未定义行为（UB）                |
| 使用已释放的指针          | `delete p; *p = 5;`               | 悬空指针，访问非法内存           |
| delete 和 new[] 混用 | `delete arr;` 替代 `delete[] arr;` | UB，析构函数不会被正确调用      |

---



## 5. 底层机制

- `new` 底层调用的是 `operator new`，通常实现为 `malloc` 或系统调用（如 `sbrk`, `mmap`）
- `delete` 调用 `operator delete`，释放内存后调用析构函数

```cpp
void* operator new(size_t size);     // 分配原始内存
void operator delete(void* ptr);     // 释放原始内存
```

可以重载 `operator new/delete` 来跟踪内存使用。



