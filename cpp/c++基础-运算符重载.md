# c++基础-运算符重载

## 1. 运算符重载

**运算符重载** 允许你为自定义类型定义特定运算符的行为，比如：

```cpp
Complex a(1, 2), b(3, 4);
Complex c = a + b; // 自定义 Complex 类支持加法运算
```

> 本质上是将运算符变成函数调用，比如：`a + b` ⇒ `a.operator+(b)` 或 `operator+(a, b)`

---

## 2. 语法格式

### 成员函数方式：

```cpp
class A {
public:
    A operator+(const A& rhs) const;
};
```

### 非成员函数方式（友元或全局）：

```cpp
class A {
    friend A operator+(const A& lhs, const A& rhs);
};
```

> 一元运算符建议写成成员函数；二元运算符可以两种都行。

---

## 3. 常用可重载运算符

| 运算符 | 含义     | 可重载 | 说明                          |
|--------|----------|-----|-------------------------------|
| `+`    | 加法     | 可以  | 最常用，二元操作符            |
| `-`    | 减法     | 可以   | 一元 & 二元                   |
| `*`    | 乘法     | 可以    | 同样支持点乘矩阵等            |
| `==`   | 比较     | 可以    | 推荐重载 `!=` 一起             |
| `[]`   | 下标     |  可以   | 类似数组访问                  |
| `()`   | 函数调用 |  可以   | 让对象像函数一样调用          |
| `<<`   | 输出流   |   可以  | 常用于 `std::ostream` 输出     |
| `>>`   | 输入流   |   可以  | 常用于 `std::istream` 输入     |
| `=`    | 赋值     | 可以    | 注意深浅拷贝                   |
| `->`   | 指针访问 |  可以   | 智能指针场景常用               |

---

## 4. 示例

```cpp
#include <iostream>
class Complex {
    double real, imag;
public:
    Complex(double r, double i): real(r), imag(i) {}

    // 成员函数重载 +
    Complex operator+(const Complex& rhs) const {
        return Complex(real + rhs.real, imag + rhs.imag);
    }

    // 输出重载（用友元函数）
    friend std::ostream& operator<<(std::ostream& os, const Complex& c) {
        os << c.real << " + " << c.imag << "i";
        return os;
    }
};

int main() {
    Complex a(1, 2), b(3, 4);
    Complex c = a + b;
    std::cout << c << std::endl;  // 输出：4 + 6i
}
```

---

## 5. 一元运算符重载

```cpp
class Counter {
    int value;
public:
    Counter(int v): value(v) {}

    Counter operator++() {  // 前置 ++
        ++value;
        return *this;
    }

    Counter operator++(int) { // 后置 ++（int用于区分）
        Counter temp = *this;
        value++;
        return temp;
    }
};
```


