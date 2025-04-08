# c++基础-list

## 1. `std::list`

`std::list` 是 C++ STL 中的**双向链表**容器，每个元素都是一个节点，节点之间通过指针连接。

> 底层结构：**双向链表（Doubly Linked List）**

---

## 2. 特点总结

| 特性                | 说明 |
|---------------------|------|
| 双向链表结构         | 每个节点有前后指针，支持双向遍历  
| 插入/删除效率高      | 任意位置插入/删除元素 O(1)，无需移动其他元素  
| 不支持随机访问       | `list[i]` 不支持，必须用迭代器逐个访问  
| 可包含重复元素       | 它允许重复值  
| 自动管理内存         | 插入/删除节点时自动处理内存分配

---

## 3. 定义与初始化

```cpp
#include <list>

std::list<int> l1;                        // 空 list
std::list<int> l2 = {1, 2, 3};            // 初始化 list
std::list<int> l3(5, 100);                // 5 个 100
```

---

## 4. 示例

### 插入元素

```cpp
l.push_back(10);       // 尾部插入
l.push_front(20);      // 头部插入

auto it = l.begin();
std::advance(it, 1);
l.insert(it, 30);      // 在第2个位置插入30
```

### 删除元素

```cpp
l.pop_back();          // 删除尾部
l.pop_front();         // 删除头部

auto it = l.begin();
std::advance(it, 2);
l.erase(it);           // 删除指定位置元素

l.remove(30);          // 删除所有值为 30 的元素
```

### 访问元素

```cpp
l.front();             // 第一个元素
l.back();              // 最后一个元素
```

> `std::list` 不支持 `operator[]`，不能随机访问。

---

## 5. 遍历 list

### 使用范围 for 循环：

```cpp
for (const auto& val : l) {
    std::cout << val << " ";
}
```

### 使用迭代器：

```cpp
for (auto it = l.begin(); it != l.end(); ++it) {
    std::cout << *it << " ";
}
```

---

## 6. 其它操作

```cpp
l.size();              // 元素个数
l.empty();             // 是否为空
l.clear();             // 清空
l.reverse();           // 反转顺序
l.sort();              // 排序（默认升序）
```

---

## 7. 自定义排序

```cpp
l.sort([](int a, int b) {
    return a > b; // 降序
});
```

---

## 8. 链表专属操作

### `splice`: 把另一个 list 插入到当前 list 某个位置

```cpp
std::list<int> l1 = {1, 2, 3};
std::list<int> l2 = {4, 5, 6};

auto it = l1.begin();
std::advance(it, 2);
l1.splice(it, l2); // 把 l2 插到 l1 的第 3 个位置
// l1 = {1, 2, 4, 5, 6, 3}
// l2 = 空
```

