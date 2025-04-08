# c++基础-结构体

结构体（`struct`）是 C/C++ 中一种用于定义自定义数据类型的方式。结构体可以将不同类型的数据（例如整数、浮点数、字符等）组织在一起，形成一个新的复合数据类型。它广泛应用于需要存储多个相关数据的场景。

## 1. 定义结构体
在 C/C++ 中，结构体使用 `struct` 关键字来定义。一个结构体可以包含多个不同类型的成员（成员变量）。

### 基本语法：
```cpp
struct StructName {
    type member1;
    type member2;
    // ...
};
```

### 示例：
```cpp
struct Person {
    char name[50];
    int age;
    float height;
};
```
在这个例子中，定义了一个名为 `Person` 的结构体，它包含三个成员：
- `name`：一个长度为50的字符数组，用于存储姓名。
- `age`：一个整数，用于存储年龄。
- `height`：一个浮点数，用于存储身高。

## 2. 结构体的声明和初始化
结构体的声明和初始化可以通过以下两种方式进行。

### 直接声明并初始化：
```cpp
Person person1 = {"John", 25, 175.5};
```

### 使用点操作符访问成员：
```cpp
person1.age = 30;
person1.height = 180.0;
```

## 3. 结构体的指针

可以通过指针来访问结构体的成员。结构体指针需要使用箭头操作符 (`->`) 来访问成员，而不是点操作符 (`.`)。

### 示例：
```cpp
Person* ptr = &person1;
ptr->age = 35;  // 修改 person1 的 age 成员
```

## 4. 结构体数组

可以创建结构体数组，用于存储多个结构体实例。

### 示例：
```cpp
Person people[3];  // 创建一个包含3个Person对象的数组
people[0].age = 20;
people[1].age = 25;
```

## 5. 结构体内存布局

C/C++ 结构体的内存布局不是严格按照声明的顺序排列的。为了提高性能，编译器通常会在成员之间插入填充字节（padding）来确保数据按对齐规则排列。例如，如果某个成员的对齐要求较高（例如 `double` 类型的对齐为8字节），编译器会在该成员之前插入空白字节，以确保结构体成员的地址是按对齐要求分配的。

### 示例：
```cpp
struct Example {
    char a;   // 1字节
    double b; // 8字节
};
```
在这个例子中，结构体可能会在 `char a` 和 `double b` 之间插入7个字节的填充字节（具体取决于编译器和平台）。

## 6. 结构体的对齐
结构体的对齐是由其最大成员的对齐需求决定的。对齐的目的是提高内存访问的效率。在不同的平台上，结构体的对齐方式可能不同。

### 使用 `#pragma pack` 控制对齐：
可以使用 `#pragma pack` 指令来修改结构体成员的对齐方式，减少填充字节的数量，但可能会影响性能。

```cpp
#pragma pack(push, 1)  // 强制对齐方式为1字节
struct Example {
    char a;   // 1字节
    double b; // 8字节
};
#pragma pack(pop)  // 恢复默认对齐
```

## 7. 结构体的大小
结构体的大小可以通过 `sizeof` 运算符来获取。

### 示例：
```cpp
std::cout << "Size of Person: " << sizeof(Person) << std::endl;
```
这将输出结构体 `Person` 的字节大小。

## 8. 结构体的默认构造函数

在 C++ 中，结构体默认提供一个构造函数，但它并没有参数。如果需要自定义构造函数，可以在结构体中声明。

### 示例：
```cpp
struct Person {
    char name[50];
    int age;
    float height;

    Person(const char* n, int a, float h) {  // 自定义构造函数
        strcpy(name, n);
        age = a;
        height = h;
    }
};

Person p1("John", 25, 175.5);  // 使用自定义构造函数
```

## 9. 结构体与类的区别
C++ 中的结构体与类 (`class`) 的区别在于：
- 默认情况下，结构体的成员是 **public**，而类的成员是 **private**。
- 除了这个访问控制的默认差异外，结构体和类在 C++ 中基本相同，都可以拥有构造函数、析构函数、成员函数等。

### 示例：
```cpp
struct MyStruct {
    int x;  // public by default
};

class MyClass {
    int y;  // private by default
};
```

## 10. 结构体的其他特性
- **匿名结构体**：结构体也可以不指定名称，称为匿名结构体。
```cpp
struct {
    int x;
    int y;
} point;
```

- **嵌套结构体**：结构体可以嵌套定义其他结构体。
```cpp
struct Address {
    char street[100];
    int zip;
};

struct Person {
    char name[50];
    Address address;
};
```


