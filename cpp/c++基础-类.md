# c++基础-类

**类** 是对一类对象的抽象，**对象（Object）** 是类的实例。类可以包含：
- **成员变量**（属性/字段）
- **成员函数**（方法）
- 构造函数、析构函数
- 静态成员
- 友元函数
- 访问控制（public/protected/private）
- 继承、多态等特性

---

## 1. 类的定义与使用

### 1.1 定义一个类
```cpp
class Person {
public:
    // 成员变量
    std::string name;
    int age;

    // 成员函数
    void introduce() {
        std::cout << "My name is " << name << ", I am " << age << " years old." << std::endl;
    }
};
```

### 1.2 创建对象并使用
```cpp
Person p;
p.name = "Alice";
p.age = 25;
p.introduce();  // 输出：My name is Alice, I am 25 years old.
```

---

## 2. 构造函数 & 析构函数

### 2.1 构造函数（Constructor）

用于创建对象时自动调用，可重载。

```cpp
class Person {
public:
    std::string name;
    int age;

    Person(std::string n, int a) {  // 构造函数
        name = n;
        age = a;
    }
};
```

### 2.2 析构函数（Destructor）

对象销毁时调用，常用于资源释放。

```cpp
~Person() {
    std::cout << "Destructor called for " << name << std::endl;
}
```

---

## 3. 访问控制（public / private / protected）

| 修饰符     | 说明                                 |
|------------|--------------------------------------|
| `public`   | 外部可以访问                          |
| `private`  | 只有类的成员函数和友元能访问          |
| `protected`| 类本身和子类可以访问                  |

```cpp
class Demo {
private:
    int secret;

public:
    void set(int val) { secret = val; }
    int get() { return secret; }
};
```

---

## 4. 成员函数的 const、inline、静态

### 4.1 const 成员函数

表示这个函数不会修改成员变量。

```cpp
class Person {
    std::string name;
public:
    Person(std::string n): name(n) {}
    void printName() const {
        std::cout << name << std::endl;
    }
};
```

### 4.2 static 静态成员

属于类而不是对象，无需实例化即可访问。

```cpp
class Math {
public:
    static int square(int x) {
        return x * x;
    }
};

int val = Math::square(5);  // 25
```

---

## 5. 类的高级特性

### 5.1 友元函数 / 友元类
```cpp
class Box {
private:
    int width;

    // 友元函数
    friend void printWidth(Box);
};

void printWidth(Box b) {
    std::cout << b.width << std::endl;
}
```

### 5.2 this 指针
类的成员函数内部使用 `this` 指针指向当前对象。
```cpp
class Test {
    int x;
public:
    Test(int x) {
        this->x = x;
    }
};
```

---

## 6. 继承（Inheritance）

C++ 支持单继承、多继承、虚继承。

```cpp
class Animal {
public:
    void speak() {
        std::cout << "Animal speaking" << std::endl;
    }
};

class Dog : public Animal {
public:
    void bark() {
        std::cout << "Dog barking" << std::endl;
    }
};

Dog d;
d.speak();  // 继承自Animal
d.bark();   // 自己的函数
```

---

## 7. 多态（Polymorphism）

### 7.1 虚函数与重写

```cpp
class Base {
public:
    virtual void show() { std::cout << "Base" << std::endl; }
};

class Derived : public Base {
public:
    void show() override { std::cout << "Derived" << std::endl; }
};
```

### 7.2 多态调用
```cpp
Base* b = new Derived();
b->show();  // 输出：Derived（虚函数才有多态）
```

---

## 8. 类的其他特性

### 8.1 内联函数

函数定义在类内，默认就是 `inline`。

```cpp
class Demo {
public:
    void say() { std::cout << "Hi\n"; }  // 默认inline
};
```

### 8.2 类中定义的类型
```cpp
class Outer {
public:
    class Inner {
        int data;
    };
};
```


## 9. 拷贝构造函数

拷贝构造函数是一种特殊的构造函数，其作用是：**用一个已有对象去初始化另一个新对象**。

语法格式：

```cpp
ClassName(const ClassName& other);
```

---

### 9.1 拷贝构造函数的调用时机

#### 拷贝构造函数会在以下场景自动调用：

1. **用一个对象初始化另一个对象：**
   ```cpp
   MyClass a;
   MyClass b = a;  // 触发拷贝构造
   ```

2. **将对象作为实参传值给函数：**
   ```cpp
   void func(MyClass obj);  // 传值调用拷贝构造
   ```

3. **函数返回局部对象时（按值返回）：**
   ```cpp
   MyClass create() {
       MyClass temp;
       return temp;  // 返回时可能触发拷贝构造
   }
   ```

### 9.2 默认拷贝构造函数与用户定义的区别

#### 默认版本（compiler 提供）：

若你未手写拷贝构造函数，编译器会自动生成一个**浅拷贝**版本：
```cpp
class MyClass {
public:
    int x;
};

MyClass a;
MyClass b = a;  // 自动生成的拷贝构造函数逐成员复制
```

#### 用户自定义（控制行为）：
当类含有资源（如指针、句柄）时，应实现自定义拷贝构造函数：
```cpp
class MyClass {
    int* data;
public:
    MyClass(int val) { data = new int(val); }

    // 拷贝构造函数：实现深拷贝
    MyClass(const MyClass& other) {
        data = new int(*other.data);
    }

    ~MyClass() { delete data; }
};
```

---

### 9.3 深拷贝 vs 浅拷贝

| 类型     | 说明                                                | 举例                    |
|----------|-----------------------------------------------------|-------------------------|
| 浅拷贝   | 复制指针地址，两个对象共享同一块资源                | 默认拷贝构造函数        |
| 深拷贝   | 复制内容，为新对象重新分配资源                      | 自定义拷贝构造函数      |

> 如果类管理动态内存，**一定要自定义拷贝构造函数**，否则析构时会出现 **double free**。

---

### 9.4 配套规则

如果自定义了下列任意一个,大概率要自定义其他的：

- 拷贝构造函数
- 拷贝赋值运算符 `operator=`
- 析构函数
- 移动构造函数（`MyClass(MyClass&&)`）
- 移动赋值运算符（`operator=(MyClass&&)`）


### 9.5 拷贝构造函数底层机制

#### 编译器生成的默认版本等价于：
```cpp
ClassName::ClassName(const ClassName& other) {
    this->a = other.a;
    this->b = other.b;
    ...
}
```

#### 编译器在栈上生成对象时，会直接通过拷贝构造调用构造语义：
```cpp
MyClass a;
MyClass b(a);       // 调用拷贝构造
MyClass c = a;      // 也会调用拷贝构造，而不是赋值
```

---

### 9.6 禁用拷贝构造函数

若希望对象**不可拷贝**（如单例类、资源封装类等）：

#### C++11 方式（推荐）：
```cpp
class NonCopyable {
public:
    NonCopyable() = default;
    NonCopyable(const NonCopyable&) = delete;
    NonCopyable& operator=(const NonCopyable&) = delete;
};
```

## 10.移动构造函数

移动构造函数是 C++11 引入的一种 **资源“转移”语义的构造函数**，用于 **避免昂贵的深拷贝**，尤其适用于含资源的类。

语法格式：

```cpp
ClassName(ClassName&& other);
```

- 参数类型是右值引用（`T&&`）
- 该构造函数的目的是 **"窃取资源" 而不是复制**

---

### 10.1 移动构造函数的好处

- 避免昂贵的深拷贝

#### 拷贝构造函数的瓶颈

```cpp
class Buffer {
    char* data;
public:
    Buffer(size_t size) {
        data = new char[size];
    }
    ~Buffer() { delete[] data; }

    Buffer(const Buffer& other) {
        // 深拷贝：昂贵！
        data = new char[strlen(other.data)];
        strcpy(data, other.data);
    }
};
```

若 `Buffer` 被频繁返回、传参，会导致多次动态分配，**性能低下**。

---

#### 移动构造：0 拷贝！只转移指针

```cpp
Buffer(Buffer&& other) {
    this->data = other.data;
    other.data = nullptr;  // 清空来源，避免析构冲突
}
```

---

### 10.2 移动构造函数调用场景

以下情境自动调用 **移动构造函数（优先于拷贝）**：

| 场景 | 说明 |
|------|------|
| 函数返回局部对象（按值返回） | 若未被 RVO 消除，则移动 |
| `std::move(obj)` | 将左值强制转换为右值，触发移动 |
| 容器内部重分配（如 `std::vector::emplace_back`） | 使用移动而非拷贝 |

> RVO(Return Value Optimization ) 优化允许编译器直接在调用者提供的内存空间中构造函数要返回的对象，从而完全避免了中间临时对象的创建和拷贝操作。

### 示例：
```cpp
Buffer makeBuffer() {
    Buffer buf(100);
    return buf;  // 移动构造（若编译器不做 RVO）
}

Buffer a = makeBuffer();  // 移动
Buffer b = std::move(a);  // 强制移动
```

---

### 10.3 移动构造函数示例

```cpp
class Buffer {
    char* data;
    size_t size;

public:
    Buffer(size_t sz) : size(sz) {
        data = new char[sz];
        std::cout << "Constructed\n";
    }

    // 移动构造函数
    Buffer(Buffer&& other) noexcept
        : data(other.data), size(other.size) {
        other.data = nullptr;  // 防止 double free
        other.size = 0;
        std::cout << "Moved\n";
    }

    ~Buffer() {
        delete[] data;
        std::cout << "Destructed\n";
    }
};
```

> 推荐加 `noexcept`：很多 STL 容器在内部判断 move 是否安全，`noexcept` 可提升性能。
noexcept 是 C++11 引入的一个异常说明符 (exception specifier)。它用于声明一个函数是否会抛出异常。
---

### 10.4 右值引用（T&&）与 std::move 本质

- `T&&` 是右值引用 —— 只能绑定右值
- `std::move(obj)` 并非移动，是 **将左值强制转为右值**，从而触发移动构造或移动赋值

### 例子：

```cpp
Buffer a(100);
Buffer b = std::move(a);  // 触发移动构造函数
```

---

### 10.5 移动构造函数的注意事项

| 项 | 说明 |
|----|------|
| 必须处理原对象资源 | 通常设为 `nullptr` |
| 推荐使用 `noexcept` | STL 容器如 `std::vector` 依赖于此 |
| 移动后对象仍需可析构 | 但状态“空”了 |
| 若类中有不可移动成员，则不能默认生成移动构造 |

---

### 10.6 默认和禁用

#### 默认生成（如果没有用户定义拷贝/移动）：
```cpp
class A {
public:
    std::string s;
    // 自动生成移动构造函数（因为 string 可移动）
};
```

#### 禁用移动构造函数（如禁止资源窃取）：
```cpp
class A {
    A(A&&) = delete;
};
```

---



