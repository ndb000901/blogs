# c++基础-枚举

枚举是一种**用户自定义类型**，由一组**命名的整型常量**组成，用于提高代码的可读性和可维护性。

---

## 1. 传统枚举（C 风格）

### 1.1 定义语法：

```cpp
enum 枚举名 { 枚举值1, 枚举值2, ..., 枚举值N };
```

### 1.2 示例：

```cpp
enum Color { RED, GREEN, BLUE };

Color c = RED;
```

### 1.3 枚举值默认从 `0` 开始递增：

```cpp
enum Weekday { MON = 1, TUE, WED }; // TUE = 2, WED = 3
```

### 1.4 可显式指定值：

```cpp
enum Status { OK = 200, NOT_FOUND = 404, ERROR = 500 };
```

---

## 2. 枚举底层类型

默认底层类型是 `int`。但可以强制指定（C++11起）：

```cpp
enum Status : unsigned short { OK = 1, FAIL = 2 };
```

---

## 3. 强类型枚举（`enum class`，C++11 起）

### 特点：

| 特性 | `enum` | `enum class` |
|------|--------|--------------|
| 命名空间污染 | 是 | 否（作用域限制） |
| 隐式转换为整型 | 可以 | 不可以 |
| 类型安全 | 否 | 是 |
| 可指定底层类型 | C++11起可以 | 是 |

### 示例：

```cpp
enum class Direction { Left, Right, Up, Down };

Direction d = Direction::Left;
```

使用 `enum class` 时，**必须加作用域限定符**，例如 `Direction::Left`


