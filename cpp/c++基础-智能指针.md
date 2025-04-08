# c++基础-智能指针

## 1. 智能指针

**智能指针** 是一个类模板，模拟普通指针的行为，并且能在生命周期结束时自动释放资源。  
本质上是用 **对象的生命周期** 来管理 **动态内存**，避免 `new` / `delete` 混乱。

也就是说：

> **不用手动调用 `delete`，对象超出作用域就自动释放了。**

---

## 2. C++ 标准库提供的智能指针

| 智能指针     | 引入版本 | 特点                             |
|--------------|----------|----------------------------------|
| `std::unique_ptr` | C++11     | 独占所有权，不能拷贝，仅能移动        |
| `std::shared_ptr` | C++11     | 引用计数，多个指针共享同一资源        |
| `std::weak_ptr`   | C++11     | 不拥有资源，用于打破 shared_ptr 环引用 |

---

## 3. `unique_ptr` —— 独占式智能指针

### 特点

- 独占资源，不能复制，只能**移动**（`std::move`）
- 适用于**资源明确唯一拥有者**的情况

### 示例

```cpp
#include <memory>

void test_unique_ptr() {
    std::unique_ptr<int> uptr(new int(10));
    *uptr = 20;
    // std::unique_ptr<int> up2 = uptr; 错误：不能拷贝
    std::unique_ptr<int> up2 = std::move(uptr);  // 正确：移动
}
```

### make_unique 推荐用法（C++14）

```cpp
auto up = std::make_unique<int>(42);
```

---

## 4. `shared_ptr` —— 引用计数式智能指针

### 特点

- 多个指针**共享同一块内存**
- 内部使用**引用计数**机制：当引用数为 0 自动释放资源
- 适合资源需要多个对象共享的场景

### 示例

```cpp
#include <memory>
#include <iostream>

void test_shared_ptr() {
    std::shared_ptr<int> sp1 = std::make_shared<int>(100);
    std::shared_ptr<int> sp2 = sp1;  // 引用计数 +1

    std::cout << "count: " << sp1.use_count() << std::endl; // 输出 2
}
```

---

## 5. `weak_ptr` —— 弱引用指针

### 特点

- **不参与引用计数**，不拥有资源
- 主要用于解决 `shared_ptr` 引用环问题
- 需使用 `.lock()` 获取临时 `shared_ptr`

### 示例

```cpp
#include <memory>
#include <iostream>

void test_weak_ptr() {
    std::shared_ptr<int> sp = std::make_shared<int>(100);
    std::weak_ptr<int> wp = sp;

    if (auto temp = wp.lock()) {  // 判断资源是否还活着
        std::cout << *temp << std::endl;
    }
}
```

---

## 6. shared_ptr 循环引用问题

```cpp
struct B;
struct A {
    std::shared_ptr<B> bptr;
};

struct B {
    std::shared_ptr<A> aptr;
};
```

这种写法将导致资源**永远不会释放**（互相持有引用 count ≠ 0）

### 正确方式

```cpp
struct B;
struct A {
    std::shared_ptr<B> bptr;
};

struct B {
    std::weak_ptr<A> aptr;  // 改为 weak_ptr
};
```

---


## 7. 智能指针底层实现

- 重载 `*`, `->` 操作符：模拟指针行为
- 析构函数自动 `delete` 对象
- 引用计数维护（`shared_ptr`）
- 使用 `make_shared` / `make_unique` 提高效率 & 安全性

---

## 8. 陷阱与注意事项

### `shared_ptr<T>` 和裸指针 `T*` **不能混用释放资源**

- `std::shared_ptr` 内部维护着一个引用计数器，记录有多少个 `shared_ptr` 指向同一个对象。当最后一个指向该对象的 `shared_ptr` 销毁时，它会负责调用 `delete` (或者你提供的自定义删除器) 来释放内存。
- 如果你先通过一个 `shared_ptr` 管理了内存，然后又通过一个裸指针 `delete` 了这块内存，那么当 `shared_ptr` 自身销毁时，它会再次尝试 `delete` 已经被释放的内存，导致双重释放 (double free) 错误，这是一个严重的未定义行为，通常会导致程序崩溃。
- 同样，如果你先通过裸指针 `new` 分配了内存，然后创建了一个 `shared_ptr` 来管理它，之后你又通过另一个裸指针 `delete` 了这块内存，那么 `shared_ptr` 会持有一个悬挂指针 (dangling pointer)。当 `shared_ptr` 销毁时，它会尝试 `delete` 已经无效的内存，同样导致未定义行为。

### `shared_ptr` 不适合大对象频繁拷贝的场景（引用计数有性能开销）

- 每次拷贝 `std::shared_ptr` 对象时，其内部的引用计数器都需要进行原子操作（通常是原子增量）。当 `shared_ptr` 对象销毁时，引用计数器需要进行原子减量。只有当引用计数变为零时，才会真正释放所管理的资源。
- 原子操作通常比普通的操作要慢，因为它们需要保证在多线程环境下的线程安全性。
- 对于小的对象，这种开销通常可以忽略不计。但是，如果对象非常大，拷贝的成本本身就很高，再加上频繁的引用计数操作，可能会对性能产生可见的影响。

### 避免用 `shared_ptr` 管理 `this` 指针，除非使用 `enable_shared_from_this`

**原因解释：**
- 假设你已经通过一个 `std::shared_ptr` `ptr1` 管理了一个 `MyClass` 的对象。现在，在该对象的某个成员函数中，你又创建了一个新的 `std::shared_ptr` `ptr2(this)`.
- `ptr1` 和 `ptr2` 是**独立的** `shared_ptr` 对象，它们各自维护着自己的引用计数，并且都认为自己拥有该对象的管理权。
- 当 `ptr1` 的引用计数变为零时，它会 `delete` 该对象。
- 之后，当 `ptr2` 的引用计数变为零时，它会再次尝试 `delete` 已经被释放的对象，从而导致双重删除的未定义行为。

**解决方案：使用 `std::enable_shared_from_this`**
- 为了安全地获取指向当前对象的 `std::shared_ptr`，你需要让你的类继承自 `std::enable_shared_from_this<MyClass>`。
- 这个基类提供了一个成员函数 `shared_from_this()`，它会返回一个新的 `std::shared_ptr`，该指针**与最初管理该对象的 `shared_ptr` 共享同一个引用计数**。


```cpp
#include <iostream>
#include <memory>

class MyClass : public std::enable_shared_from_this<MyClass> {
public:
    std::shared_ptr<MyClass> getSharedPtr() {
        return shared_from_this();
    }

    void printAddress() const {
        std::cout << "Object address: " << this << std::endl;
    }

    ~MyClass() {
        std::cout << "MyClass destroyed." << std::endl;
    }
};

int main() {
    std::shared_ptr<MyClass> ptr1 = std::make_shared<MyClass>();
    ptr1->printAddress();

    std::shared_ptr<MyClass> ptr2 = ptr1->getSharedPtr();
    ptr2->printAddress();

    std::cout << "ptr1 use count: " << ptr1.use_count() << std::endl;
    std::cout << "ptr2 use count: " << ptr2.use_count() << std::endl;

    return 0;
}
```

**注意：** 只有当对象已经被一个 `std::shared_ptr` 管理时，才能安全地调用 `shared_from_this()`。如果在没有被 `shared_ptr` 管理的对象上调用它，会导致未定义行为（通常会抛出 `std::bad_weak_ptr` 异常）。








