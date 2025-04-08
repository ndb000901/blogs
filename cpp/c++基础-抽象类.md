# c++基础-抽象类

## 1. 抽象类

**抽象类** 是指 **包含至少一个纯虚函数的类**，它不能实例化，只能被继承。  
用于规定“子类必须实现的接口”。

### 纯虚函数（Pure Virtual Function）

定义形式如下：

```cpp
virtual void func() = 0;
```

这个 `= 0` 就表示这是一个 **纯虚函数**。

---

## 2. 抽象类的作用

- **规范接口**：像 Java 的接口一样，定义一套标准接口。
- **实现多态**：通过父类指针/引用调用子类的具体实现。
- **禁止实例化**：抽象类不能直接创建对象，强制使用子类。

---

## 3. 抽象类与继承

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void speak() = 0;  // 纯虚函数
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "Dog barks!" << endl;
    }
};

int main() {
    // Animal a; // 错误：不能实例化抽象类
    Animal* p = new Dog();  // 多态
    p->speak();             // 调用的是 Dog 的 speak()
    delete p;
}
```

---

## 4. 抽象类的继承规则

子类必须实现所有纯虚函数，否则它自己也是抽象类

```cpp
class A {
public:
    virtual void f() = 0;
};

class B : public A {
    // 没有实现 f，B 也是抽象类
};

class C : public A {
public:
    void f() override { cout << "C::f()\n"; }
};
```

---

## 5. 抽象类作为接口使用

在 C++ 中，用抽象类来实现接口风格（类似 Java 的 interface）：

```cpp
class Printable {
public:
    virtual void print() = 0;
    virtual ~Printable() {}  // 接口类建议写虚析构函数
};

class Report : public Printable {
public:
    void print() override {
        cout << "Printing report...\n";
    }
};
```

---

## 6. 抽象类可以有普通函数和成员变量

抽象类不等于完全空壳，它可以包含：

- 普通成员变量
- 非虚函数
- 虚函数（非纯虚）
- 构造函数 / 析构函数

```cpp
class Base {
public:
    void hello() { cout << "Hello\n"; }
    virtual void show() = 0;
};
```

---

## 7. 虚析构函数的重要性

当通过**父类指针删除子类对象**，一定要加虚析构函数，否则可能造成资源泄露。

```cpp
class Base {
public:
    virtual ~Base() { cout << "Base析构\n"; }
};

class Child : public Base {
public:
    ~Child() { cout << "Child析构\n"; }
};

int main() {
    Base* p = new Child();
    delete p;  // 正确调用 Child 和 Base 的析构函数
}
```

**不使用虚析构函数**

1.  **静态绑定：** 当你使用 `delete` 关键字删除一个指针时，编译器会根据指针的**静态类型**（声明时的类型）来决定调用哪个类的析构函数。

2.  **父类指针指向子类对象：** 如果你有一个指向子类对象的父类指针（通过向上转型获得），那么 `delete` 操作会调用**父类的析构函数**，而不是子类的析构函数。

3.  **子类可能持有额外资源：** 子类通常会扩展父类的功能，并且可能在自己的构造函数中分配了额外的资源（例如，通过 `new` 分配的内存、打开的文件句柄、获取的锁等）。这些资源需要在子类的析构函数中进行清理和释放。

4.  **资源泄露：** 如果只调用了父类的析构函数，而没有调用子类的析构函数，那么子类在其构造函数中分配的那些额外资源就不会被释放，从而导致**资源泄露**。最常见的就是**内存泄漏**，但也可以是文件句柄未关闭、锁未释放等。

**使用虚析构函数**

1.  **动态绑定：** 当基类的析构函数被声明为 `virtual` 时，`delete` 操作的行为会发生改变。编译器会使用**动态绑定**（通过虚函数表）来确定指针实际指向的对象的类型，并调用该对象**实际类型**的析构函数。

2.  **析构函数链：** 当你 `delete` 一个通过基类指针指向的子类对象时，如果基类的析构函数是虚函数，那么：
    * 首先，会调用**子类的析构函数**，释放子类特有的资源。
    * 然后，会自动调用**父类的析构函数**，释放父类持有的资源。

3.  **确保完整清理：** 这样就保证了从最派生的类开始，沿着继承链向上，每个类的析构函数都会被调用，从而确保所有由对象分配的资源都能得到正确的释放，避免了资源泄露。


---

## 8. 纯虚析构函数也要定义实现体！

```cpp
class Abstract {
public:
    virtual ~Abstract() = 0;  // 纯虚析构函数
};

Abstract::~Abstract() {
    // 必须实现，否则链接报错
}
```

尽管纯虚函数通常意味着没有默认的实现，但析构函数是一个例外。
即使你将基类的析构函数声明为纯虚函数，你也必须为其提供一个实现体。
这是因为在销毁派生类对象时，派生类的析构函数会隐式地调用基类的析构函数，而链接器需要找到这个基类析构函数的定义。
如果缺失这个定义，链接过程就会失败。

---

