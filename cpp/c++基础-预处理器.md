# c++基础-预处理器


## 1. 预处理器

预处理器是 **编译前的文本替换阶段**，作用于源代码文件，在编译器实际处理代码之前，执行一系列“文本级”的替换和处理操作。

> 本质：**纯文本处理工具**
>  
> 阶段：编译器前、编译器不执行它，`g++ -E` 可查看预处理结果

---

## 2. 常见预处理指令（以 `#` 开头）

### 2.1 `#include` —— 引入头文件

```cpp
#include <iostream>   // 系统头文件
#include "myhead.h"   // 用户头文件（优先从当前目录查找）
```

预处理阶段会直接将整个头文件内容插入进来。

---

### 2.2 `#define` —— 宏定义（文本替换）

#### 定义常量

```cpp
#define PI 3.14159
#define VERSION "v1.0"
```

#### 定义带参数的宏（函数宏）

```cpp
#define SQUARE(x) ((x)*(x))
#define MAX(a,b) ((a) > (b) ? (a) : (b))
```

**注意加括号！** 不加括号容易出现运算错误。

#### 宏取消定义

```cpp
#undef PI
```

---

### 2.3 `#ifdef / #ifndef / #if / #else / #elif / #endif` —— 条件编译

```cpp
#define DEBUG

#ifdef DEBUG
  std::cout << "Debug mode\n";
#endif
```

#### 更复杂判断

```cpp
#if VERSION >= 2
  // 编译这段
#else
  // 编译那段
#endif
```

---

### 2.4 `#pragma` —— 编译器指令（非标准）

常见的用法：

```cpp
#pragma once      // 防止头文件重复包含（可替代 include guard）
#pragma GCC optimize("O3")  // GCC 特有优化指令
```

---

### 2.5 `#error` / `#warning` —— 编译时报错/警告信息

```cpp
#error "This file requires C++20 support"
```

---

## 3. 头文件保护机制

### 3.1 Include Guard（传统）

```cpp
#ifndef MY_HEADER_H
#define MY_HEADER_H

// 头文件内容

#endif
```

### 3.2 `#pragma once`（现代推荐）

```cpp
#pragma once
```

优点：简单，不易出错，编译更快（编译器内部优化）

---

## 4. 宏与常量的区别

| 对比项           | 宏 (`#define`)  | 常量 (`const`)         |
|------------------|------------------|-------------------------|
| 类型安全         | 无类型           | 有类型检查               |
| 调试时可见性     | 不可见           | 可调试                 |
| 编译阶段         | 预处理阶段         | 编译阶段                |
| 推荐程度         | 少用            | 建议使用 `const`, `constexpr` |

---

## 5. 查看预处理结果

```bash

g++ -E main.cpp -o main.i
```

> 输出文件是预处理结果（宏已展开，头文件已展开）

---


## 6. 高级技巧

### 6.1 字符串化宏参数：`#`

```cpp
#define TO_STRING(x) #x
std::cout << TO_STRING(hello world);  // 输出 "hello world"
```

### 6.2 拼接宏参数：`##`

```cpp
#define MAKE_VAR(name) int var_##name
MAKE_VAR(foo);  // 展开为 int var_foo;
```

### 6.3 利用宏定义日志输出

```cpp
#define LOG(msg) std::cout << "[LOG] " << msg << " @ " << __FILE__ << ":" << __LINE__ << "\n";
```

---
