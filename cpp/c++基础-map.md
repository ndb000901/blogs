# c++基础-map

 `std::map` —— C++ STL 中一个非常重要的**关联容器（Associative Container）**。它通过 **键值对（key-value）** 组织数据，支持 **自动排序、快速查找**，非常适合实现字典、配置项、索引映射等功能。

---

## 1. `std::map`

- 是一个 **有序的键值对集合**
- key **唯一**，自动按 key 排序（默认 `operator<`）
- 基于 **红黑树（平衡二叉搜索树）实现**，查找、插入、删除时间复杂度为 O(log N)

头文件：`#include <map>`

---

## 2. 基本定义方式

```cpp
std::map<std::string, int> ageMap;
```

- `std::map<KeyType, ValueType>`：键类型 + 值类型

### 初始化

```cpp
std::map<std::string, int> m = {
    {"Tom", 18},
    {"Jerry", 20},
    {"Alice", 19}
};
```

---

## 3.示例

### 插入元素

```cpp
m["Bob"] = 22;                         // 自动插入新 key，value 是 22
m.insert({"Charlie", 25});            // 使用 pair 插入
m.emplace("David", 30);               // C++11，更高效地原地构造
```

### 访问元素

```cpp
int age = m["Tom"];                   // 若不存在，自动创建 value 为 0
int age2 = m.at("Jerry");             // 若不存在会抛出异常 std::out_of_range
```

### 查找元素

```cpp
if (m.find("Tom") != m.end()) {
    std::cout << "Tom exists\n";
}
```

### 删除元素

```cpp
m.erase("Bob");                       // 删除 key 为 "Bob" 的元素
m.erase(m.begin());                   // 删除迭代器指定的元素
m.clear();                            // 清空所有元素
```

---

## 4. 遍历方式

```cpp
for (const auto& pair : m) {
    std::cout << pair.first << ": " << pair.second << '\n';
}

for (auto it = m.begin(); it != m.end(); ++it) {
    std::cout << it->first << ": " << it->second << '\n';
}
```

---


## 5. 自定义排序规则（key排序）

```cpp
struct DescOrder {
    bool operator()(const int& a, const int& b) const {
        return a > b; // 降序
    }
};

std::map<int, std::string, DescOrder> myMap;
```

> 也可以用 lambda 表达式作为比较器（C++20 支持）

---

## 6. 和 `unordered_map` 的对比

| 特性               | `std::map`   | `std::unordered_map` |
|--------------------|--------------|--------------------|
| 是否有序           | 有序         | 无序（hash 表）     |
| 查找/插入复杂度    | O(log N)     | O(1) 平均           |
| 内存使用           | 较少         | 较多（hash + 链表）  |
| 迭代顺序           | 按 key 升序   | 不确定顺序           |
| 自定义排序         | 支持         | 不支持             |

`map`：适合需要顺序、范围查询  
`unordered_map`：适合性能优先、无顺序要求

---

## 7. 线程安全

- 多线程访问：**读安全**，**写或读写需要加锁**
- 推荐使用 `std::mutex` 保护

---

