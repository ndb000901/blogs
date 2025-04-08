# c++基础-流程控制

## 1. 条件语句详解

### 1.1 `if` / `else if` / `else`

#### 基本结构：
```cpp
if (condition) {
    // 满足时执行
} else if (other_condition) {
    // 第二个条件
} else {
    // 上述都不满足时执行
}
```

#### 示例：
```cpp
int x = 10;
if (x > 0)
    std::cout << "positive";
else if (x == 0)
    std::cout << "zero";
else
    std::cout << "negative";
```

---

### 1.2 `switch-case` 多分支结构

#### 基本结构：
```cpp
switch (expression) {
    case value1:
        // 匹配 value1 时执行
        break;
    case value2:
        // 匹配 value2 时执行
        break;
    default:
        // 没有匹配时执行
}
```

#### 示例：
```cpp
int day = 3;
switch (day) {
    case 1: std::cout << "Mon"; break;
    case 2: std::cout << "Tue"; break;
    case 3: std::cout << "Wed"; break;
    default: std::cout << "Unknown";
}
```

> 注意：`switch` 支持 **整数、枚举、字符**，不支持浮点或字符串。

---



## 2. 跳转语句详解

### 2.1 `break` — 退出循环或 `switch`
```cpp
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    std::cout << i << " ";
}
```

---

### 2.2 `continue` — 跳过当前循环剩余部分
```cpp
for (int i = 0; i < 5; i++) {
    if (i == 2) continue;
    std::cout << i << " ";
}
// 输出：0 1 3 4
```

---

### 2.3 `return` — 返回值或退出函数
```cpp
int sum(int a, int b) {
    return a + b;
}
```

---

### 2.4 `goto` — 跳转到标签（不推荐）
```cpp
int x = 1;
goto skip;
x = 100;
skip:
std::cout << x;  // 输出：1
```

> 极度不推荐使用 `goto`，破坏结构化流程，可读性差。

---

