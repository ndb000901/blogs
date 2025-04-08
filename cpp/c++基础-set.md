# c++基础-set

## 1. `std::set`

`std::set` 是 C++ STL 中的一个**关联容器**，用于存储**唯一的元素**，并根据键的比较规则自动排序。  
> 底层实现：**红黑树（self-balancing binary search tree）**

---

## 2. 基本特性

| 特性             | 说明 |
|------------------|------|
| 元素唯一         | 每个元素只能出现一次  
| 自动排序         | 默认使用 `<`，升序  
| 元素只存储 key   | `set` 只存 key，没有 value  
| 操作效率         | 查找、插入、删除 O(log N)  
| 元素不可修改     | 插入后不能直接修改元素值（会破坏排序）

---

## 3. 定义与初始化

### 定义
```cpp
#include <set>

std::set<int> s;
```

### 初始化
```cpp
std::set<int> s = {3, 1, 4, 1, 5, 9};  // 自动去重 + 排序
// s = {1, 3, 4, 5, 9}
```

---

## 4. 示例

### 插入元素

```cpp
s.insert(10);              // 插入成功
s.insert(3);               // 已存在，插入失败（无重复）
```

`insert` 返回 `pair<iterator, bool>` 表示插入结果：

```cpp
auto [it, success] = s.insert(42);
if (success) {
    std::cout << "插入成功: " << *it << "\n";
}
```

### 删除元素

```cpp
s.erase(3);                // 按值删除
s.erase(s.begin());        // 按迭代器删除
```

### 查找元素

```cpp
if (s.find(4) != s.end()) {
    std::cout << "找到了 4\n";
}
```

### 清空/大小

```cpp
s.clear();                 // 清空
s.size();                  // 元素个数
s.empty();                 // 是否为空
```

---

## 5. 遍历 `set`

```cpp
for (const auto& x : s) {
    std::cout << x << " ";
}
```

也可以用迭代器：

```cpp
for (auto it = s.begin(); it != s.end(); ++it) {
    std::cout << *it << " ";
}
```

---

## 6. 自定义排序规则

默认是升序排序（使用 `<`），也可以自定义比较器，比如降序：

```cpp
struct Desc {
    bool operator()(int a, int b) const {
        return a > b;
    }
};

std::set<int, Desc> s2 = {1, 3, 2};
// s2: 3, 2, 1
```

---

## 7. 查找函数

```cpp
s.count(x);              // 存在返回 1，不存在返回 0
s.lower_bound(x);        // ≥ x 的第一个元素迭代器
s.upper_bound(x);        // > x 的第一个元素迭代器
```

---

