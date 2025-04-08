# c++基础-接口

在 C++ 中没有像 Java 那样显式的 `interface` 关键字，但可以通过**抽象类（abstract class）+ 纯虚函数（pure virtual function）** 来实现“接口”的概念。


---

## 1.接口

**接口 = 一组行为的定义（没有实现）**

在 C++ 中，接口的本质就是：
- 一个只有**纯虚函数**的抽象类
- 用于强制派生类去实现某些行为
- 常用于 **多态（polymorphism）** 场景

---

## 2. 接口的定义方式（用抽象类模拟）

```cpp
class IShape {
public:
    virtual void draw() = 0;   // 纯虚函数
    virtual ~IShape() = default; // 虚析构函数（推荐）
};
```

### 关键点：
- `= 0` 表示纯虚函数
- 没有成员变量
- 通常名字前加 `I` 前缀表示接口（非强制）
- 析构函数为虚函数：避免派生类析构不完整

---

## 3. 接口的实现方式（类实现接口）

```cpp
class Circle : public IShape {
public:
    void draw() override {
        std::cout << "Draw Circle\n";
    }
};
```

- 使用 `override` 表示重写接口函数（C++11）
- 编译器可帮你检查签名匹配

---

## 4. 接口使用示例（多态）

```cpp
void render(IShape* shape) {
    shape->draw();  // 多态调用
}

int main() {
    Circle c;
    render(&c);  // 输出：Draw Circle
}
```

- 用接口参数抽象行为
- 调用时由运行时类型决定行为（虚函数表机制）

---

## 5. 接口可以有多个（多接口继承）

```cpp
class ISerializable {
public:
    virtual std::string serialize() = 0;
    virtual ~ISerializable() = default;
};

class IShape {
public:
    virtual void draw() = 0;
    virtual ~IShape() = default;
};

class Rectangle : public IShape, public ISerializable {
public:
    void draw() override { std::cout << "Draw Rect\n"; }
    std::string serialize() override { return "rect{}"; }
};
```

支持多接口继承（不像 Java 是单继承类 + 多接口）

---

## 6. 接口指针 / 引用用法（解耦）

```cpp
void printSerialized(const ISerializable& obj) {
    std::cout << obj.serialize();
}
```

优势：
- 解耦调用方和具体实现类
- 适合做策略模式 / 工厂模式 / 依赖注入

---



## 7. 接口的底层原理：虚函数表（vtable）

- 每个类有一个虚函数表（指向函数指针数组）
- 每个对象内部保存指向虚表的指针
- 调用虚函数时，查表调用正确的函数

```cpp
shape->draw();
// 等价于：shape->vptr->vtable[0](shape)
```

