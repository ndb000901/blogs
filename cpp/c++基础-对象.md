# c++基础-对象


## 1. 对象

对象是 **类的实例**，它占据内存空间，具备类所定义的所有属性和行为。  
通俗一点说：

> 类是模板，对象是用模板制造出来的东西。

### 举个例子：

```cpp
class Person {
public:
    std::string name;
    int age;

    void sayHello() {
        std::cout << "Hello, I am " << name << std::endl;
    }
};

int main() {
    Person p1;  // 创建对象
    p1.name = "Tom";
    p1.age = 20;
    p1.sayHello();  // 调用对象的方法
}
```

---

## 2. 对象的内存结构

对象在内存中主要包含：
1. **非静态成员变量**（实际存在于每个对象中）
2. **函数指针表指针（vptr）**（如果有虚函数）
3. **不包含非静态成员函数本身**，这些函数只有一份代码

### 举例：

```cpp
class A {
    int x;        // 每个对象独有
    static int y; // 所有对象共享，存放在全局区
};
```

---

## 3. 对象的创建方式

### 3.1 栈上创建（最常见）

```cpp
Person p;
```

自动释放，生命周期随着作用域结束。

### 3.2 堆上创建（使用 `new`）

```cpp
Person* p = new Person();
// ...
delete p;
```

手动释放，配合 `delete` 使用。

### 3.3 静态对象

```cpp
static Person p;
```

程序结束时才析构，生命周期比普通局部变量长。

### 3.4 动态数组对象

```cpp
Person* arr = new Person[10];
// ...
delete[] arr;
```

---

## 4. 对象的构造与析构

对象创建时，自动调用**构造函数**；销毁时，自动调用**析构函数**。

```cpp
class Test {
public:
    Test() { std::cout << "构造\n"; }
    ~Test() { std::cout << "析构\n"; }
};
```

如果你堆上创建了对象，一定记得调用 `delete` 释放。

---

## 5. 对象的拷贝、赋值与销毁

### 5.1 拷贝构造函数（复制对象时自动调用）

```cpp
Person p1("Tom", 18);
Person p2 = p1;  // 拷贝构造
```

> 如果不自定义，编译器会默认生成浅拷贝（成员逐个拷贝）。但如果有资源（如指针），建议自己写 **深拷贝**。

### 5.2 赋值操作符 `operator=`

```cpp
Person p1, p2;
p1 = p2;  // 调用赋值运算符
```

---

## 6. 对象与 this 指针

每个成员函数在执行时，都会隐式接收一个 `this` 指针，指向当前对象。

```cpp
class Person {
    int age;
public:
    void setAge(int age) {
        this->age = age;  // 区分局部变量和成员变量
    }
};
```

---

## 7. 对象的传值与引用

### 7.1 值传递（会调用拷贝构造）
```cpp
void printPerson(Person p);  // 拷贝一份
```

### 7.2 引用传递（高效，推荐）
```cpp
void printPerson(const Person& p);  // 不拷贝，不修改
```

---

## 8. 匿名对象

```cpp
class A {
public:
    A() { std::cout << "构造\n"; }
    ~A() { std::cout << "析构\n"; }
};

A();  // 临时创建一个对象，马上销毁
```

---

## 9. 类对象 vs 指针对象 vs 引用对象

| 类型              | 示例              | 说明                           |
|-------------------|-------------------|--------------------------------|
| 普通对象          | `Person p;`       | 存储在栈上，自动析构           |
| 指针对象          | `Person* p = new Person;` | 存储在堆上，需手动 delete |
| 引用对象          | `Person& ref = p;`| 对已有对象的别名，不占新内存   |

---

