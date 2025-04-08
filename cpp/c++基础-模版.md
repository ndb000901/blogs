# c++基础-模版

## 1. 模板

模板是 C++ 提供的一种**编写与类型无关代码**的机制，分为两种：

- **函数模板（Function Template）**
- **类模板（Class Template）**

它的作用是：**写一次代码，适用于多种数据类型。**

---

## 2. 函数模板

### 定义方式

```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}
```

也可以使用 `class` 关键字代替 `typename`，等价：

```cpp
template <class T>
T add(T a, T b);
```

### 使用示例

```cpp
int main() {
    cout << add<int>(3, 4) << endl;        // 指定类型
    cout << add(3.5, 2.5) << endl;         // 类型推导
}
```

### 注意
- 模板函数支持类型推导；
- 但当类型不一致或推导失败时，必须显示指定类型；
- 可以使用多个模板参数，如 `template <typename T, typename U>`

---

## 3. 类模板详解

### 定义方式

```cpp
template <typename T>
class MyArray {
private:
    T arr[100];
public:
    void set(int index, T value) { arr[index] = value; }
    T get(int index) const { return arr[index]; }
};
```

### 使用示例

```cpp
int main() {
    MyArray<int> a;
    a.set(0, 42);
    cout << a.get(0) << endl;

    MyArray<string> b;
    b.set(0, "hello");
    cout << b.get(0) << endl;
}
```

---

## 4. 类模板中定义成员函数的两种方式

### 方法一：类内定义

```cpp
template <typename T>
class Demo {
public:
    void print() { cout << "Hello\n"; }
};
```

### 方法二：类外定义

```cpp
template <typename T>
class Demo {
public:
    void print();
};

template <typename T>
void Demo<T>::print() {
    cout << "Hello outside\n";
}
```

注意要加上 `template <typename T>` 和 `Demo<T>::`

---

## 5. 模板的参数种类

1. 类型参数（常见）：
   ```cpp
   template <typename T>
   ```
2. 非类型参数（值）：
   ```cpp
   template <int size>
   class Buffer { char data[size]; };
   ```
3. 模板模板参数：
   ```cpp
    #include <iostream>
    #include <vector>
    #include <list>
    #include <algorithm>
    
    // 定义一个模板函数，它接受一个容器类型作为模板模板参数
    template <template<typename> class Container, typename T>
    void printContainer(const Container<T>& cont) {
        std::cout << "Container elements: ";
        for (const T& item : cont) {
            std::cout << item << " ";
        }
        std::cout << std::endl;
    }
    
    int main() {
        std::vector<int> vec = {1, 2, 3, 4, 5};
        std::list<double> lst = {1.1, 2.2, 3.3};
    
        // 使用 std::vector 实例化模板函数
        printContainer<std::vector, int>(vec);
    
        // 使用 std::list 实例化模板函数
        printContainer<std::list, double>(lst);
    
        return 0;
    }
   ```

---

## 6. 模板特化

模板特化 (Template Specialization) 是 C++ 模板的一个特性，它允许你为特定的模板参数（类型或非类型）提供一个专门的实现。

### 全特化

当你为模板的所有参数都指定了具体的类型或值时，就称为全特化。

```cpp
template <>
class MyArray<bool> {
    // 为 bool 专门实现版本
};
```

### 偏特化

偏特化允许你为类模板的部分模板参数指定具体的类型或值，而其他模板参数仍然保持通用。

```cpp
template <typename T, typename U>
class Pair;

template <typename T>
class Pair<T, int> {
    // 第二个参数为 int 的特化版本
};
```

---

## 7. 函数模板重载与特化

函数模板可以重载，也可以特化。

```cpp
template <typename T>
void print(T val) {
    cout << "Generic: " << val << endl;
}

template <>
void print<int>(int val) {
    cout << "Specialized for int: " << val << endl;
}
```

---




