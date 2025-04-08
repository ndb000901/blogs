# c++基础-多态

## 1. 多态

**多态：同一种接口，表现出不同的行为。**

在 C++ 中，通常指通过基类指针/引用调用派生类重写的虚函数，从而实现动态绑定（runtime dispatch）。

---

## 2. 实现多态的三要素

1. 有继承
2. 有虚函数（`virtual`）
3. 用基类指针或引用调用

### 示例：
```cpp
class Animal {
public:
    virtual void speak() { std::cout << "Animal\n"; }
};

class Dog : public Animal {
public:
    void speak() override { std::cout << "Dog\n"; }
};

void makeSound(Animal& a) {
    a.speak();  // 动态绑定
}
```

---

## 3. 静态多态 vs 动态多态

| 类型        | 实现方式                     | 绑定时机       | 例子                    |
|-------------|------------------------------|----------------|-------------------------|
| 静态多态    | 函数重载 / 模板              | 编译期绑定     | `operator+`, 模板       |
| 动态多态    | 虚函数 + 继承                | 运行时绑定     | `virtual`, override    |

---

## 4. 虚函数表（vtable）

C++ 编译器通过 **虚函数表（vtable）** 实现动态多态：

- 每个含虚函数的类都有一张 vtable。
- 每个对象都有一个 **vptr**（虚表指针），指向该类的 vtable。
- vtable 中存的是虚函数的地址。

```cpp
Animal a;        // vptr → Animal vtable → Animal::speak
Dog d;           // vptr → Dog vtable → Dog::speak
```

当我们写：

```cpp
Animal* p = new Dog;
p->speak();      // 虚表查找 → Dog::speak()
```

编译器实际生成代码类似：
```cpp
(*p->vptr[0])();  // 调用虚表第一个函数
```

---

## 5. 虚函数使用注意事项

### 用 `override` 修饰派生类重写的虚函数
```cpp
class Dog : public Animal {
public:
    void speak() override;  // 防止拼写错误
};
```

### 用 `final` 禁止进一步重写
```cpp
class Cat : public Animal {
    void speak() override final;
};
```

### 构造函数/析构函数中别调用虚函数

构造/析构期间，vptr 仍指向当前类的 vtable，**不是派生类的**。

---

## 6. 纯虚函数和抽象类

```cpp
class Animal {
public:
    virtual void speak() = 0;  // 纯虚函数
};
```

含纯虚函数的类是 **抽象类**，不能实例化。用于设计接口（interface）。

---


## 7. 面试常问问题

### Q1: 多态的本质是啥？  
本质是通过虚表（vtable）进行动态分发，达到 **运行时决定行为** 的效果。

---

### Q2: C++ 中函数重载和多态的区别？  
- 重载是静态多态（编译时选择函数）
- 虚函数是动态多态（运行时选择函数）

---

### Q3: 为什么析构函数要设为 virtual？  
防止通过基类指针析构派生类时未调用派生类析构，导致资源泄漏。

```cpp
class Base {
public:
    virtual ~Base() {}
};
```

---

### Q4: 虚函数影响性能吗？  
有少量性能开销（一次间接调用），但通常 **换来的是代码可扩展性和灵活性**，值！

---
