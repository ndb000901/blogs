# c++基础-priority_queue


## 1. `std::priority_queue`

- 是一种**基于堆结构（默认为大顶堆）** 的容器适配器；
- 支持插入元素（`push`）、删除堆顶元素（`pop`）、访问堆顶（`top`）；
- 用于高效管理“优先级”数据，比如任务调度、路径搜索、贪心算法等。

---

## 2. 基本语法

```cpp
#include <queue>

std::priority_queue<int> pq;  // 默认大顶堆（最大元素优先）
```

---

## 3. 常用操作

| 操作 | 方法 | 说明 |
|------|------|------|
| 插入元素 | `pq.push(x)` | 把元素插入堆中 |
| 访问堆顶 | `pq.top()` | 返回当前最大（或最小）元素 |
| 删除堆顶 | `pq.pop()` | 移除最大（或最小）元素 |
| 判断是否为空 | `pq.empty()` | 是否没有元素 |
| 元素个数 | `pq.size()` | 当前队列大小 |

---

## 4. 默认行为：大顶堆

```cpp
std::priority_queue<int> pq;

pq.push(3);
pq.push(5);
pq.push(1);

while (!pq.empty()) {
    std::cout << pq.top() << " "; // 输出：5 3 1
    pq.pop();
}
```

---

## 5. 小顶堆

通过指定比较函数（`std::greater<T>`）或自定义比较器。

```cpp
// 方法1：小顶堆
std::priority_queue<int, std::vector<int>, std::greater<int>> min_pq;
```

```cpp
min_pq.push(3);
min_pq.push(5);
min_pq.push(1);

while (!min_pq.empty()) {
    std::cout << min_pq.top() << " "; // 输出：1 3 5
    min_pq.pop();
}
```

---

## 6. 存放自定义结构体

### 示例：按照任务优先级调度

```cpp
struct Task {
    int id;
    int priority;

    // 重载小于号（默认是大顶堆）
    bool operator<(const Task& other) const {
        return priority < other.priority; // 优先级高的排在前
    }
};

std::priority_queue<Task> taskQueue;
taskQueue.push({1, 5});
taskQueue.push({2, 8});
taskQueue.push({3, 3});

while (!taskQueue.empty()) {
    auto t = taskQueue.top();
    std::cout << "Task " << t.id << " priority: " << t.priority << "\n";
    taskQueue.pop();
}
```

---

## 7. 自定义比较器方式（适用于复杂排序）

```cpp
struct Task {
    int id;
    int deadline;
};

struct Compare {
    bool operator()(const Task& a, const Task& b) {
        return a.deadline > b.deadline; // 提前 deadline 的优先
    }
};

std::priority_queue<Task, std::vector<Task>, Compare> pq;
```

---

## 8. 底层实现原理

- `std::priority_queue` 底层是 **`std::vector` + `make_heap`** 实现的；
- 插入时调用 `push_heap`，删除时调用 `pop_heap`；
- 所以插入、删除操作时间复杂度都是 **`O(log n)`**，访问堆顶是 **`O(1)`**。

---


