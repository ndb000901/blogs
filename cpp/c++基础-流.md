# c++基础-流

## 1. 流（Stream）

流是 C++ 输入/输出的核心抽象，代表**有方向的数据流动通道**。

> 类似“水流”一样，数据从某处流入（输入）或流出（输出）程序。

### 两种方向
- `输入流（istream）`：从设备或文件等 → 程序
- `输出流（ostream）`：从程序 → 屏幕、文件、网络等

---

## 2. C++标准流类型

| 类型             | 说明                           | 常用对象           |
|------------------|----------------------------------|--------------------|
| `istream`        | 输入流（基类）                   | `cin`, `ifstream`  |
| `ostream`        | 输出流（基类）                   | `cout`, `ofstream` |
| `iostream`       | 输入 + 输出流                    | `fstream`          |
| `istringstream`  | 从字符串中读取数据               |                    |
| `ostringstream`  | 向字符串中写入数据               |                    |
| `stringstream`   | 既能输入也能输出字符串           |                    |
| `ifstream`       | 从文件读取                       |                    |
| `ofstream`       | 向文件写入                       |                    |
| `fstream`        | 文件读写                         |                    |

---

## 3. 示例

### 3.1 控制台输入输出（cin / cout）

```cpp
#include <iostream>
using namespace std;

int main() {
    int x;
    cout << "请输入数字: ";
    cin >> x;
    cout << "你输入的是: " << x << endl;
    return 0;
}
```

### 3.2 文件读写（ifstream / ofstream / fstream）

```cpp
#include <fstream>
using namespace std;

int main() {
    ofstream out("test.txt");
    out << "Hello 文件！";
    out.close();

    ifstream in("test.txt");
    string content;
    in >> content;
    cout << content;  // 输出：Hello
}
```

> 注意：`>>` 是按空格读取，整行读取建议用 `getline(in, line);`

---

### 3.3 字符串流（sstream）

```cpp
#include <sstream>
using namespace std;

int main() {
    stringstream ss;
    ss << "123 456";

    int a, b;
    ss >> a >> b;
    cout << a + b;  // 输出：579
}
```

- 常用于：**格式解析**、**构造字符串**、**字符串与基本类型之间转换**。

---

## 4. 流的底层原理

- 所有流类都继承自 `ios_base`
- `iostream` 继承自 `istream` 和 `ostream`
- 实际读写是通过缓冲区 `streambuf` 实现的

```cpp
ostream& operator<<(ostream& out, int val);  // 实质是格式化输出
```

---

## 5. 流状态检测（错误处理）

使用 `.fail()` / `.eof()` / `.good()` / `.bad()` 检查流状态：

```cpp
if (cin.fail()) {
    cout << "输入错误！";
    cin.clear();        // 清除错误状态
    cin.ignore(1000, '\n'); // 丢弃缓冲区
}
```

---

## 6. 流的重定向

```cpp
freopen("input.txt", "r", stdin);   // 把 cin 绑定到文件输入
freopen("output.txt", "w", stdout); // 把 cout 绑定到文件输出
```

也可以临时使用 `std::stringstream` 或 `std::ostringstream` 捕获输出。

---



