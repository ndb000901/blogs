# c++基础-deque


## 1. `std::deque`

`std::deque`（Double Ended QUEue）是 **双端队列**，是 C++ STL 中的序列容器之一。它支持在容器**两端高效插入和删除元素**。

### 特点：

| 特性                  | 描述 |
|----------------------|------|
| 支持随机访问          | 像数组一样用 `[]` 访问 |
| 支持两端高效插入删除   | `push_front`, `push_back` |
| 动态扩展              | 自动扩容（无需手动管理） |
| 多块连续内存组成      | 非单块内存（区别于 vector）|

---

## 2. 基本用法

```cpp
#include <deque>
#include <iostream>

std::deque<int> dq;

dq.push_back(1);     // 尾部插入
dq.push_front(0);    // 头部插入
dq.pop_back();       // 尾部删除
dq.pop_front();      // 头部删除

dq[0];               // 随机访问
dq.at(0);            // 带边界检查的访问
```

---


## 3. 底层结构与性能

### 内存模型：
`deque` 是由多个连续内存块组成的二维结构（块表 + 块数据），块表中每一项指向实际存储的元素块。

这使得：
- 它**支持双端高效操作**；
- 但中间插入不是很高效。

### 插入效率（时间复杂度）：

| 操作          | 时间复杂度 |
|---------------|------------|
| `push_back`   | O(1) 均摊  |
| `push_front`  | O(1) 均摊  |
| `insert` 中间 | O(n)       |
| `[]`          | O(1)       |

---

## 4. 示例：滑动窗口最大值

这是一个经典 deque 应用场景（性能远好于暴力）：

```cpp
#include <deque>
#include <vector>
#include <iostream>

std::vector<int> maxSlidingWindow(std::vector<int>& nums, int k) {
    std::deque<int> dq;
    std::vector<int> result;

    for (int i = 0; i < nums.size(); ++i) {
        // 清理出窗口的元素
        if (!dq.empty() && dq.front() == i - k)
            dq.pop_front();

        // 移除不可能成为最大值的元素
        while (!dq.empty() && nums[dq.back()] < nums[i])
            dq.pop_back();

        dq.push_back(i);

        if (i >= k - 1)
            result.push_back(nums[dq.front()]);
    }

    return result;
}
```

---

## 5. 注意事项

- **遍历方式**：
  ```cpp
  for (auto& val : dq) { /*...*/ }
  for (size_t i = 0; i < dq.size(); ++i) { std::cout << dq[i]; }
  ```

- **指针/引用可能失效**：插入/删除操作可能会重新分配内部块，迭代器或引用会失效。

- **内存占用略高**：因为分块管理会带来一点内存碎片。

---
