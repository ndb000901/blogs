# c++基础-stack


## 1. `std::stack`

`std::stack` 是 C++ STL 提供的**栈容器适配器（container adapter）**，遵循 **后进先出（LIFO）** 原则：  
> 最后放进去的元素最先被取出。

它**不是**独立容器，而是建立在其它容器（如 `deque` 或 `vector`）之上的接口封装。

---

## 2. 栈的特点（LIFO）

```
push(1)
push(2)
push(3)
栈顶 -> 3 2 1
pop() 后变成 -> 2 1
```

- **只允许从顶部插入和移除元素**
- **不支持随机访问**

---

## 3. 基本语法

```cpp
#include <stack>

std::stack<int> s;
s.push(10);     // 压栈
s.top();        // 返回栈顶元素
s.pop();        // 弹栈（移除栈顶）
s.empty();      // 是否为空
s.size();       // 栈中元素个数
```

---


## 4. 指定底层容器

默认底层容器是 `std::deque`，可以自定义为 `std::vector`：

```cpp
std::stack<int, std::vector<int>> s1;
std::stack<std::string, std::deque<std::string>> s2;
```

---

## 5. 遍历 stack

`stack` **不能直接遍历**，只能通过不断 `top + pop`：

```cpp
std::stack<int> s;
s.push(1); s.push(2); s.push(3);

while (!s.empty()) {
    std::cout << s.top() << " ";
    s.pop();
}
// 输出：3 2 1
```

如果你需要遍历但不破坏数据，请用**拷贝栈**：

```cpp
std::stack<int> s_copy = s;
```


