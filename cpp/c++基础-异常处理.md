# c++基础-异常处理

**异常处理机制（Exception Handling）**，它是 C++ 提供的一种**处理运行时错误**的方式，避免了程序因为错误而中止，让你可以优雅地处理各种“意外”。

---

## 1. 异常处理

C++ 使用 `try`、`throw` 和 `catch` 三个关键字实现异常处理机制：

```cpp
try {
    // 可能抛出异常的代码
} catch (异常类型1 e1) {
    // 处理异常1
} catch (异常类型2 e2) {
    // 处理异常2
}
```

---

## 2. 异常处理流程

1. 在 `try` 块中执行代码；
2. 如果遇到 `throw` 抛出异常；
3. 异常匹配到合适的 `catch` 块；
4. 执行 `catch` 中代码；
5. 如果没有匹配的 `catch`，则调用 `std::terminate()`，程序终止。

---

## 3. 示例

```cpp
#include <iostream>
using namespace std;

int divide(int a, int b) {
    if (b == 0) throw runtime_error("Division by zero!");
    return a / b;
}

int main() {
    try {
        int result = divide(10, 0);
        cout << result << endl;
    } catch (const runtime_error& e) {
        cout << "Caught exception: " << e.what() << endl;
    }
    return 0;
}
```

输出：

```
Caught exception: Division by zero!
```

---

## 4. throw 的多种使用方式

```cpp
throw 42;                        // 整数
throw "error";                   // C风格字符串
throw std::string("error");      // string对象
throw std::runtime_error("bad"); // 标准异常类
```

---

## 5. catch 的匹配机制

按照出现顺序匹配，类型必须能被转换。

```cpp
try {
    throw 3.14;
} catch (int x) {
    // 不匹配
} catch (double x) {
    // 匹配成功
}
```

可以使用引用捕获以避免 slicing：

> Slicing (对象切片) 是 C++ 中在使用对象进行赋值或按值传递时，当源对象是派生类的实例，而目标对象是基类的实例时发生的一种现象。
> 派生类对象中特有的数据成员会被“切掉”，只剩下基类部分的数据。

```cpp
catch (const std::exception& e) {
    // 推荐：引用捕获
}
```

---

## 6. 标准异常类体系（头文件 `<stdexcept>`）

C++ STL 提供了一套标准异常类：

```cpp
std::exception
├── std::logic_error
│   ├── invalid_argument
│   ├── domain_error
│   ├── length_error
│   └── out_of_range
└── std::runtime_error
    ├── range_error
    ├── overflow_error
    └── underflow_error
```

可以使用 `.what()` 方法获取异常信息：

```cpp
catch (const std::exception& e) {
    std::cout << e.what();
}
```

---

## 7. 异常安全与资源管理

C++ 强调 RAII 原则，结合异常使用可以确保**资源自动释放**。

**RAII 的原理：**

RAII(Resource Acquisition Is Initialization) 是一种编程技术，它将资源的生命周期与对象的生命周期绑定在一起。具体来说：

1.  **资源获取 (Acquisition) 在对象的构造函数中进行。**
2.  **资源释放 (Release) 在对象的析构函数中进行。**

当一个 RAII 对象离开其作用域时（无论是正常离开还是由于异常抛出），它的析构函数都会被自动调用，从而保证了资源的释放。

**如何使用 RAII 实现类似 `finally` 的功能：**

你可以创建一个类，在该类的构造函数中获取你需要管理的资源，并在其析构函数中释放这些资源。然后，在 `try` 块中创建这个类的对象。无论 `try` 块中的代码是否抛出异常，当该对象离开作用域时，其析构函数都会被调用，从而保证了资源的释放。

### 示例1

```cpp
#include <iostream>
#include <fstream>
#include <stdexcept>

class FileCloser {
private:
    std::ofstream& file;
public:
    FileCloser(std::ofstream& f) : file(f) {}
    ~FileCloser() {
        if (file.is_open()) {
            std::cout << "Closing file." << std::endl;
            file.close();
        }
    }
};

void processFile(const std::string& filename) {
    std::ofstream outfile(filename);
    if (!outfile.is_open()) {
        throw std::runtime_error("Could not open file");
    }

    FileCloser closer(outfile); // RAII 对象，确保文件在离开作用域时关闭

    try {
        outfile << "Some data to write to the file." << std::endl;
        // 模拟可能抛出异常的情况
        throw std::runtime_error("Simulated error during file processing");
        outfile << "This line will not be reached." << std::endl;
    } catch (const std::runtime_error& e) {
        std::cerr << "Caught exception: " << e.what() << std::endl;
        // 不需要在这里显式关闭文件，因为 FileCloser 对象的析构函数会自动完成
    }
    // FileCloser 对象 closer 在这里离开作用域，其析构函数会被调用，关闭文件
}

int main() {
    processFile("example.txt");
    return 0;
}
```

### 示例2

```cpp
class File {
public:
    File() { std::cout << "Open file\n"; }
    ~File() { std::cout << "Close file\n"; }
};

void f() {
    File f;
    throw std::runtime_error("error");
}
// 即使异常抛出，~File() 也会被自动调用
```

---

## 8. 异常规范（不推荐使用）

C++98 有一个机制可以标注函数能抛出哪些异常：

```cpp
void f() throw(std::runtime_error); // 已废弃
```

在 C++11 引入了 `noexcept` 代替：

```cpp
void f() noexcept;         // 保证不抛异常
void f() noexcept(false);  // 可以抛异常
```

---

## 9. 重新抛出异常

在 `catch` 中可以重新抛出异常：

```cpp
try {
    try {
        throw std::runtime_error("inner");
    } catch (...) {
        std::cout << "rethrow\n";
        throw; // 重新抛出当前异常
    }
} catch (const std::exception& e) {
    std::cout << "caught again: " << e.what() << '\n';
}
```

---

## 10. 异常与析构函数

> 析构函数中不应该抛出异常！

因为如果一个析构函数在栈展开过程中抛出异常，程序会终止。

建议写法：

```cpp
~MyClass() noexcept {
    try {
        // 不要抛异常
    } catch (...) {
        // 捕获并吞掉
    }
}
```

---

