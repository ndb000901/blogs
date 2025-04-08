# c++基础-unordered_set

## 1. `std::unordered_set`

`std::unordered_set` 是 C++11 引入的容器，和 `unordered_map` 类似，底层是哈希表，特点是：

- 元素 **唯一**（去重）
- 元素按 **哈希值组织**，**无序**
- 支持 **快速插入/查找/删除**（平均 O(1) 时间复杂度）

它可以看作是一个 **只存 key，没有 value 的 unordered_map**。

---

## 2. 基本语法和使用

```cpp
#include <iostream>
#include <unordered_set>
using namespace std;

int main() {
    unordered_set<int> uset;

    uset.insert(3);
    uset.insert(1);
    uset.insert(5);

    for (int val : uset) {
        cout << val << " ";
    }
    cout << endl;
}
```

输出顺序是无序的，如：`5 1 3`（哈希表特性）。

---

## 3. 常用操作

| 操作 | 示例 | 说明 |
|------|------|------|
| 初始化 | `unordered_set<int> s = {1,2,3};` | 直接用列表初始化 |
| 插入 | `s.insert(4);` | 插入元素（自动去重） |
| 查找 | `s.find(2)` | 找到返回迭代器，否则返回 `s.end()` |
| 删除 | `s.erase(2);` | 删除指定元素 |
| 清空 | `s.clear();` | 删除所有元素 |
| 大小 | `s.size();` | 当前元素数量 |
| 判空 | `s.empty();` | 判断是否为空 |
| 遍历 | `for (auto x : s)` | 遍历所有元素 |

---

## 4. 底层原理

- 底层是**哈希表（hashtable）**：
  - 由多个桶（buckets）组成。
  - 每个桶里可以存储**多个值**（通过链表/开链法处理冲突）。
- 每次插入/查找会：
  - 使用 `std::hash<T>` 生成哈希值。
  - 根据哈希值选择桶。
  - 在桶内比较是否已存在（使用 `operator==`）。

---

## 5. 自定义类型支持

要使用自定义类型，需要提供：

- 自定义 `hash` 函数；
- 重载 `==` 操作符。

### 示例：`pair<int, int>` 作为 key

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

struct PairHash {
    size_t operator()(const pair<int, int>& p) const {
        return hash<int>()(p.first) ^ (hash<int>()(p.second) << 1);
    }
};

struct PairEqual {
    bool operator()(const pair<int, int>& a, const pair<int, int>& b) const {
        return a.first == b.first && a.second == b.second;
    }
};

int main() {
    unordered_set<pair<int, int>, PairHash, PairEqual> mySet;

    mySet.insert({1, 2});
    mySet.insert({3, 4});

    for (auto& p : mySet) {
        cout << "(" << p.first << ", " << p.second << ")\n";
    }
}
```

---

## 6. 性能特性

| 操作 | 平均时间复杂度 | 最坏情况（哈希冲突） |
|------|----------------|-----------------------|
| 插入/删除/查找 | O(1) | O(n) |
| 遍历 | O(n) | O(n) |

### 注意事项

- 最坏情况是哈希冲突严重（如所有元素哈希值一样）；
- C++ STL 中默认哈希函数对简单类型（`int`, `string`, 等）优化良好。


