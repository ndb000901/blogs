# c++基础-queue


## 1. `std::queue`

`std::queue` 是 C++ STL 中的一个**容器适配器（container adapter）**，基于其他序列容器（如 `deque`）实现的 **先进先出（FIFO）** 队列结构。

### 特点：
- **先进先出**：先进入队列的元素，最先被取出。
- 只能从 **尾部入队（push）**，从 **头部出队（pop）**。
- 没有迭代器接口，无法遍历。

---

## 2. 基本语法

```cpp
#include <queue>

std::queue<int> q;

q.push(10);     // 入队
q.front();      // 查看队首元素
q.back();       // 查看队尾元素
q.pop();        // 出队（移除队首元素）
q.empty();      // 判断队列是否为空
q.size();       // 队列中的元素个数
```

---

## 3. 底层容器适配

默认容器是 `std::deque`，也可以用 `std::list`：

```cpp
std::queue<int, std::deque<int>> q1; // 默认
std::queue<int, std::list<int>> q2;
```

不支持用 `vector`，因为 vector 头部 `pop_front()` 性能差。

---


## 4. 遍历队列的方式（不能使用迭代器）

遍历时只能通过复制或 destructively pop：

```cpp
std::queue<int> q;
q.push(1); q.push(2); q.push(3);

// 复制遍历（保留原始）
std::queue<int> q_copy = q;
while (!q_copy.empty()) {
    std::cout << q_copy.front() << " ";
    q_copy.pop();
}
```

---


