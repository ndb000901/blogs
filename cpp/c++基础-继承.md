# c++基础-继承

## 1.继承

继承是面向对象编程中的核心特性之一，**子类自动拥有父类的属性和行为**，实现 **代码复用** 和 **多态机制**。

```cpp
class Base {
public:
    void sayHi() { cout << "Hi from Base" << endl; }
};

class Derived : public Base {
};
```

现在 `Derived` 就继承了 `Base` 的成员函数 `sayHi`。

---

## 2. 继承的语法形式

```cpp
class 子类 : 继承方式 父类 {
    // 子类的成员
};
```

### 常见继承方式：

| 继承方式 | 父类的 `public` 成员在子类中 | `protected` 成员 | `private` 成员 |
|----------|-------------------------------|------------------|----------------|
| `public` | `public`                     | `protected`      | 不可访问        |
| `protected` | `protected`               | `protected`      | 不可访问        |
| `private` | `private`                   | `private`        | 不可访问        |

```cpp
class A {
public:
    int x;
protected:
    int y;
private:
    int z;
};

class B : public A {
    // x 是 public
    // y 是 protected
    // z 无法访问
};
```

---

## 3. 继承的关键特性

### 3.1 子类可以访问父类的成员（权限允许）

```cpp
class Base {
protected:
    int age = 18;
};

class Derived : public Base {
public:
    void printAge() {
        cout << "Age: " << age << endl;
    }
};
```

---

### 3.2 构造函数与析构函数不能被继承

虽然子类不能继承父类构造函数，但你可以通过 `using` 显式继承：

```cpp
class A {
public:
    A(int x) {}
};

class B : public A {
public:
    using A::A;  // C++11 起允许“构造函数继承”
};
```

---

### 3.3 构造与析构的调用顺序

- **构造顺序：父 → 子**
- **析构顺序：子 → 父**

```cpp
class A {
public:
    A() { cout << "A 构造\n"; }
    ~A() { cout << "A 析构\n"; }
};

class B : public A {
public:
    B() { cout << "B 构造\n"; }
    ~B() { cout << "B 析构\n"; }
};
```

---

### 3.4 覆盖（Override）和隐藏（Hide）

```cpp
class Base {
public:
    void foo(int) {}
    virtual void bar() {}
};

class Derived : public Base {
public:
    void foo(double) {}     // 隐藏 Base::foo(int)
    void bar() override {}  // 正确覆盖
};
```

---

### 3.5 使用 `override` 和 `final` 关键字（C++11）

- `override`：显式声明是重写父类虚函数，编译器检查签名
- `final`：阻止子类重写这个函数
- `const`：承诺该函数不会修改调用它的对象的数据成员（除非数据成员被声明为 mutable）。也就是说，在这个函数内部，不能对对象的非 mutable 成员变量进行赋值操作。

```cpp
class Base {
public:
    virtual void speak() const;
};

class Dog : public Base {
public:
    void speak() const override final;
};
```

---

## 4. 多继承（Multiple Inheritance）

```cpp
class A { public: void fa(); };
class B { public: void fb(); };
class C : public A, public B {};
```

### 多继承的二义性

```cpp
class A { public: void print(); };
class B { public: void print(); };

class C : public A, public B {
public:
    void test() {
        // print(); // 错误，二义性
        A::print(); // 明确调用
    }
};
```

---

## 5. 虚继承（Virtual Inheritance）

为了解决 **菱形继承** 导致的“重复继承”问题。



```cpp
class A { public: int x = 42; };
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {}; // 只存在一份 A 的副本
```

通过在 B 和 C 继承 A 时使用关键字 virtual，可以告诉编译器只需要在最终的派生类 D 中包含 A 的一个共享实例。

---

## 6. 继承和多态的关系

- 虚函数 + 父类指针/引用 = 多态
- 父类指针可以指向子类对象
- 调用虚函数触发动态绑定（运行时多态）

```cpp
class Animal {
public:
    virtual void speak() { cout << "Animal" << endl; }
};

class Dog : public Animal {
public:
    void speak() override { cout << "Dog" << endl; }
};

void makeSound(Animal* a) {
    a->speak();  // 动态绑定
}
```

---

