# c++基础-友元


## 1. 友元

**友元（Friend）是一种打破类封装的机制**，允许非成员函数或其他类访问类的私有成员（`private`/`protected`）。

友元不是类的成员，但**可以访问类的私有和受保护成员**。

---

## 2. 友元的类型

### 2.1 **友元函数**

- 普通函数
- 全局函数
- 类的非成员函数

```cpp
class Box {
private:
    int width;
public:
    Box(int w) : width(w) {}
    friend void printWidth(Box b);  // 声明友元函数
};

void printWidth(Box b) {
    std::cout << "Width is " << b.width << std::endl; // 可以访问私有成员
}
```

---

### 2.2 **友元类**

- 一个类可以声明另一个类为友元类，让它访问自己的私有成员。

```cpp
class B; // 前向声明

class A {
private:
    int x = 42;
    friend class B; // B 是 A 的友元类
};

class B {
public:
    void showA(A& a) {
        std::cout << a.x << std::endl; // 合法访问
    }
};
```

---

### 2.3 **友元成员函数**

- 只允许**另一个类的某个成员函数**访问本类的私有成员。

```cpp
class A;

class B {
public:
    void accessA(A& a);
};

class A {
private:
    int data = 10;
    friend void B::accessA(A& a);  // 只授权 B::accessA 访问
};

void B::accessA(A& a) {
    std::cout << a.data << std::endl;
}
```

---

## 3. 友元的特点

| 特性                   | 说明                                                                 |
|------------------------|----------------------------------------------------------------------|
| **不是类成员**         | 友元函数、类不是成员，不能通过 `this` 访问调用对象                   |
| **打破封装**           | 可以访问类的私有成员，但慎用                                        |
| **单向关系**           | A 是 B 的友元 ≠ B 是 A 的友元                                       |
| **不能继承**           | 子类不会自动继承父类的友元                                          |
| **权限仍受作用域限制** | 友元的访问权限只能在声明友元的一方体现                              |


