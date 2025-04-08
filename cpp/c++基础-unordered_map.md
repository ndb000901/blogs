# c++基础-unordered_map

## 1. `std::unordered_map`

`std::unordered_map` 是 C++11 引入的一个**哈希表实现的关联容器**，提供：

- **键值对（key-value）存储**
- **常数级时间复杂度的查找、插入和删除（平均 O(1)）**
- 元素**无序排列**（按哈希值组织）
- 自动处理哈希冲突（使用链表/拉链法）

它与 `std::map` 最大的区别是底层结构：

| 容器 | 底层结构 | 是否有序 | 查找复杂度 |
|------|-----------|-------|--------------|
| `map` | 红黑树（有序） | 是 | O(logN) |
| `unordered_map` | 哈希表 | 否 | O(1) 平均，最坏 O(N) |

---

## 2. 基本使用

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

int main() {
    unordered_map<string, int> age;
    age["Tom"] = 18;
    age["Jerry"] = 20;

    cout << age["Tom"] << endl;        // 输出：18
    cout << age.at("Jerry") << endl;   // 输出：20
}
```

---

## 3. 常用操作

### 初始化

```cpp
unordered_map<string, int> m = {
    {"apple", 1},
    {"banana", 2}
};
```

### 插入元素

```cpp
m.insert({"pear", 3});
m["orange"] = 4;
```

### 查找元素

```cpp
if (m.find("apple") != m.end()) {
    cout << "Found apple" << endl;
}
```

### 删除元素

```cpp
m.erase("banana");
```

### 遍历

```cpp
for (auto& [key, value] : m) {
    cout << key << ": " << value << endl;
}
```

---

## 4. 成员函数说明

| 成员函数 | 功能 |
|----------|------|
| `insert({key, value})` | 插入元素 |
| `erase(key)` | 删除元素 |
| `find(key)` | 查找元素，返回迭代器 |
| `count(key)` | 是否存在（返回 0 或 1） |
| `clear()` | 清空 |
| `size()` | 当前元素个数 |
| `empty()` | 是否为空 |
| `at(key)` | 安全访问（无 key 会抛异常） |
| `operator[]` | 插入或访问（无 key 会自动创建） |
| `begin()/end()` | 迭代器遍历 |

---

## 5. 底层原理简述

- 底层为哈希表（桶数组 + 拉链法）
- 哈希函数：默认用 `std::hash<T>`
- 发生冲突时，同一个桶用链表/链式存储法存储多个元素
- 会自动扩容：如负载因子 > 1.0（或其他阈值），重新分配桶，重新哈希所有元素

### 自定义哈希函数示例：

```cpp
struct MyHash {
    size_t operator()(const pair<int, int>& p) const {
        return hash<int>()(p.first) ^ hash<int>()(p.second);
    }
};

unordered_map<pair<int, int>, int, MyHash> mp;
```

---

## 6. 性能分析

| 操作 | 平均时间复杂度 | 最坏情况 |
|------|----------------|-----------|
| 查找 | O(1) | O(N)（哈希冲突严重） |
| 插入 | O(1) | O(N)（重哈希或冲突） |
| 删除 | O(1) | O(N) |
| 遍历 | O(N) | O(N) |

> 实际中，只要哈希函数设计合理，性能极高。

---


