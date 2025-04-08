# c++基础-字符串

## 1. C++ 字符串

### C风格字符串（C-string）

- 类型：`char*` 或 `char[]`
- 结尾：以 `\0` 结尾（空字符）
- 缺点：容易出错，操作不安全，需要手动管理内存

```cpp
char str[] = "hello";
```

### C++ 标准字符串类（`std::string`）

- 更安全、更强大
- 自动管理内存，支持各种操作符和方法

```cpp
#include <string>
std::string s = "hello";
```

---

## 2. `std::string` 的底层机制

- 实质是一个 **模板类 `std::basic_string<char>`**
- 存储在堆上，自动管理内存
- 支持拷贝构造、移动构造、赋值、比较等操作
- 内部有**容量与长度之分**（支持容量预分配）

---

## 3. 常用操作与方法

### 初始化

```cpp
std::string s1 = "hello";
std::string s2("world");
std::string s3(5, 'x');  // s3 == "xxxxx"
```

### 连接与拼接

```cpp
std::string a = "hello";
std::string b = "world";
std::string c = a + " " + b;  // "hello world"
a += "!";  // a == "hello!"
```

### 长度与访问

```cpp
s.length();  // 或 s.size()
s[0];        // 获取字符
s.at(1);     // 安全访问，越界会抛异常
```

### 查找与子串

```cpp
std::string s = "hello world";
s.find("world");       // 返回索引 6
s.rfind("o");          // 从右往左找
s.substr(6, 5);        // "world"
```

### 替换、插入、删除

```cpp
std::string s = "hello";
s.insert(5, " world");  // "hello world"
s.replace(0, 5, "hi");  // "hi world"
s.erase(2, 3);          // 删除 2 开始 3 个字符
```

### 比较

```cpp
std::string a = "abc", b = "abd";
bool eq = (a == b);     // false
bool lt = (a < b);      // true（字典序）
```

---

## 4. C风格字符串转换

### `std::string` → `const char*`

```cpp
std::string s = "abc";
const char* cstr = s.c_str();
```

### `char*` → `std::string`

```cpp
char* cstr = "abc";
std::string s(cstr);
```

---

## 5. 输入输出

```cpp
std::string name;
std::cin >> name;             // 读到空格为止
std::getline(std::cin, name); // 读整行
std::cout << "Name: " << name << std::endl;
```

---

## 6. 性能注意事项

| 场景                 | 建议做法                                       |
|----------------------|------------------------------------------------|
| 大量拼接字符串       | 使用 `std::ostringstream` 替代 `+` 拼接         |
| 频繁增删字符         | 考虑用 `std::deque<char>` 或 `std::vector<char>` |
| 频繁改变长度         | 使用 `.reserve()` 预分配容量                   |

---

## 7. 线程安全性

- **`std::string` 本身不是线程安全的**
- 多线程中同一个 `std::string` 不应同时读写
- 可使用锁、或每线程独立拷贝副本来保障安全

---


