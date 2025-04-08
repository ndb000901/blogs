# c++基础-vector

## 1. `std::vector`

`std::vector` 是一个模板类，定义在 `<vector>` 头文件中，它提供了 **动态数组** 的功能，也就是说可以在运行时添加、删除元素，它会自动申请和释放内存。

```cpp
#include <vector>
std::vector<int> v = {1, 2, 3};
```

---

## 2. 基本语法和用法

### 定义方式

```cpp
std::vector<int> v1;                // 空 vector
std::vector<int> v2(5);             // 5 个默认初始化的元素（0）
std::vector<int> v3(5, 42);         // 5 个值为 42 的元素
std::vector<int> v4 = {1, 2, 3};    // 列表初始化
```

---

## 3. 示例

### 添加元素

```cpp
v.push_back(10);       // 末尾添加一个元素
v.emplace_back(20);    // 原地构造，效率更高（C++11）
```

---

### 访问元素

```cpp
v[0];        // 不检查越界，快
v.at(0);     // 检查越界，异常处理
v.front();   // 第一个元素
v.back();    // 最后一个元素
```

---

### 删除元素

```cpp
v.pop_back();                    // 删除最后一个
v.erase(v.begin() + 1);          // 删除指定位置
v.clear();                       // 清空所有元素
```

---

### 遍历方式

```cpp
for (int x : v) std::cout << x;

for (size_t i = 0; i < v.size(); ++i) std::cout << v[i];

for (auto it = v.begin(); it != v.end(); ++it) std::cout << *it;
```

---

### 插入和范围操作

```cpp
v.insert(v.begin() + 1, 99);               // 在第2个位置插入99
v.insert(v.end(), {4, 5, 6});              // 插入多个
v.assign(5, 100);                          // 重新赋值，5个100
```

---

## 4. 容量与内存管理

### 容量操作

```cpp
v.size();        // 当前元素个数
v.capacity();    // 当前分配的内存大小（元素数量）
v.empty();       // 是否为空

v.reserve(100);  // 提前分配内存，避免扩容
v.resize(10);    // 调整 size，新增的默认初始化
v.shrink_to_fit(); // 请求回收多余内存
```

---

### 自动扩容机制（重要）

- vector 的容量会**按倍数增长**（通常是 1.5~2 倍）
- 每次扩容会：
  1. 分配新内存
  2. 拷贝旧数据到新内存
  3. 删除旧内存

推荐：如果知道要插入多少元素，先用 `.reserve(n)` 预留容量，避免频繁 realloc。

---

## 5. 构造函数 & 拷贝行为

```cpp
std::vector<int> v1 = {1, 2, 3};
std::vector<int> v2(v1);            // 拷贝构造
std::vector<int> v3 = std::move(v1); // 移动构造
```

---

## 6. 二维 vector

```cpp
std::vector<std::vector<int>> matrix(3, std::vector<int>(4, 0));

// 3行4列，全为0
```

---

## 7. 性能相关建议

| 操作                | 建议                       |
|---------------------|----------------------------|
| 大量插入元素        | 使用 `reserve()` 预分配     |
| 不需要频繁插入删除 | 用 `vector` 性能最好        |
| 插入删除中间元素    | `list` 或 `deque` 更合适    |
| 查找访问为主        | `vector` 非常优秀           |

---

## 8. 线程安全性

- 多线程读：安全（不修改容器）
- 多线程写：不安全，需要加锁（例如 `std::mutex`）


