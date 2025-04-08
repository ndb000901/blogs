# c++基础-forward_list


## 1. `std::forward_list`

`std::forward_list` 是 C++11 引入的**单向链表（singly-linked list）**，与 `std::list`（双向链表）相比，它：

- 更**节省内存**（每个节点只保存一个指针）；
- 只支持**前向迭代器**；
- 不支持 `size()` 操作（你需要自己数）；
- 不支持反向迭代器或 `push_back()`；
- 适合**频繁头插**或在已知位置后插入的场景。

---

## 2. 基本语法

```cpp
#include <forward_list>

std::forward_list<int> flist = {1, 2, 3};
```

---

## 3. 常用操作

| 操作 | 示例 | 说明 |
|------|------|------|
| 初始化 | `forward_list<int> fl = {1,2,3};` | 列表初始化 |
| 头插 | `fl.push_front(0);` | 头部插入 |
| 头删 | `fl.pop_front();` | 移除第一个元素 |
| 插入 | `fl.insert_after(it, x);` | 在 it 之后插入 x |
| 删除 | `fl.erase_after(it);` | 删除 it 后的元素 |
| 清空 | `fl.clear();` | 移除所有元素 |
| 遍历 | `for (int x : fl)` | 范围 for 循环遍历 |
| 排序 | `fl.sort();` | 排序（默认升序） |
| 唯一 | `fl.unique();` | 移除连续重复元素 |
| 反转 | `fl.reverse();` | 倒置链表 |

---

### 示例代码：

```cpp
#include <iostream>
#include <forward_list>

int main() {
    std::forward_list<int> fl = {3, 2, 1};

    fl.push_front(4);         // [4, 3, 2, 1]
    fl.insert_after(fl.begin(), 5); // [4, 5, 3, 2, 1]
    fl.pop_front();           // [5, 3, 2, 1]

    for (int v : fl) {
        std::cout << v << " ";
    }
    std::cout << std::endl;
}
```

---

## 4. 迭代器支持

- 支持前向迭代器（`forward_iterator`）；
- **不支持随机访问（如 `fl[2]`）**；
- 可以使用 `auto` 配合 `begin()` 和 `end()`。

```cpp
for (auto it = fl.begin(); it != fl.end(); ++it) {
    std::cout << *it << " ";
}
```

---

## 5. insert_after / erase_after 用法

```cpp
std::forward_list<int> fl = {1, 2, 3};
auto it = fl.before_begin();      // 特殊位置：在头节点前
fl.insert_after(it, 0);           // 插入 0 到最前面 -> [0,1,2,3]
fl.erase_after(it);               // 删除 0 -> [1,2,3]
```

> `before_begin()` 是 forward_list 独有，用于头部插入删除。

---

## 6. 和 `std::list` 对比

| 特性 | `forward_list` | `list` |
|------|------------|----|
| 结构 | 单向链表 | 双向链表 |
| 指针数 | 每节点 1 个 | 每节点 2 个 |
| 内存占用 | 较小 | 稍大 |
| 访问方向 | 向前 | 双向 |
| 插入效率 | 更快（但仅头部和指定点后） | 快（前/后都行） |
| 支持 `size()` | 不支持 | 支持 |
| 反向迭代器 | 无 | 有 |



