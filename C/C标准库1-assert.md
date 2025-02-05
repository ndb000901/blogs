# C标准库1-assert

### `assert.h` 头文件详解

`assert.h` 是 C 语言标准库中的一个头文件，提供了 **`assert` 断言宏**，用于在调试阶段检查程序中的不变量（invariants）或假设（assumptions），以便尽早发现逻辑错误。

---

## **1. `assert` 宏的基本用法**
`assert` 宏用于检查一个表达式是否为真（非 0）。如果表达式为假（0），`assert` 会：
- 在 `stderr` 输出错误信息，包括**文件名**、**行号**和**断言失败的表达式**。
- 终止程序（调用 `abort()`）。

### **语法**
```c
#include <assert.h>

void assert(int expression);
```

### **示例**
```c
#include <assert.h>
#include <stdio.h>

int main() {
    int x = 5;
    assert(x > 0);   // 通过，程序继续执行
    assert(x < 0);   // 失败，程序终止并输出错误信息
    printf("这行不会执行\n");
    return 0;
}
```
**运行输出（当 `assert(x < 0);` 失败时）：**
```
a.out: example.c:8: main: Assertion `x < 0' failed.
Aborted (core dumped)
```
> **注意：** `assert` 失败时会终止程序，不会继续执行后续代码。

---

## **2. `assert` 的工作原理**
`assert` 宏在**编译时**会被展开成如下形式：
```c
#define assert(expr) ((expr) ? (void)0 : __assert_fail(#expr, __FILE__, __LINE__, __func__))
```
其中：
- `#expr`：字符串化宏，把 `expr` 变成字符串，如 `"x < 0"`。
- `__FILE__`：当前源文件名。
- `__LINE__`：发生断言失败的行号。
- `__func__`：当前函数名称。
- `__assert_fail()` 是 GNU C Library 提供的内部函数，它会打印错误信息并终止程序。

**等效代码**
```c
if (!(x < 0)) {
    fprintf(stderr, "%s:%d: %s: Assertion `%s` failed.\n",
            __FILE__, __LINE__, __func__, "x < 0");
    abort(); // 终止程序
}
```

---

## **3. 释放模式 (`NDEBUG`)**
在**发布版本**中，我们通常不希望 `assert` 影响性能，因此 C 语言标准允许我们定义 `NDEBUG`（No Debug），禁用所有 `assert` 语句：
```c
#define NDEBUG
#include <assert.h>
```
> **效果：** `assert(expr)` 直接被预处理器移除，不会对 `expr` 进行求值，避免运行时开销。

### **示例**
```c
#include <stdio.h>
#include <assert.h>

int main() {
    int x = -1;

    assert(x >= 0);  // 若未定义 NDEBUG，这里会终止程序

    printf("程序继续运行\n");
    return 0;
}
```
#### **不同情况下的行为**
| 代码是否定义 `#define NDEBUG` | `assert` 断言行为 |
|------------------------------|----------------|
| 未定义 `NDEBUG`               | `assert` 失败，程序终止 |
| 定义 `NDEBUG`                 | `assert` 被移除，程序正常运行 |

> **建议：** 在**调试阶段**启用 `assert`，在**正式发布时**用 `-DNDEBUG` 关闭 `assert`。

编译时使用 `gcc` 关闭 `assert`：
```sh
gcc -DNDEBUG program.c -o program
```

---

## **4. `assert` 的使用场景**
### **✅ 适合使用 `assert` 的情况**
1. **检查程序的内部不变量**（如指针、数组索引）
2. **调试时捕获意外情况**（如非法参数）
3. **开发阶段检查逻辑错误**
4. **单元测试**
5. **文档化代码假设**

**示例：防止空指针**
```c
void process(int *ptr) {
    assert(ptr != NULL);  // 确保指针不为空
    *ptr = 10;
}
```

---

### **🚫 不适合使用 `assert` 的情况**
1. **输入验证**
   - `assert` 主要用于开发调试，不应用于用户输入检查。
   - `assert` 在 `NDEBUG` 模式下会被移除，不能保证可靠性。
   - 应该使用 `if` 语句和 `exit()` 进行输入验证。

**错误示范**
```c
void read_input(int age) {
    assert(age > 0);  // ❌ 不应该用 assert
}
```
**正确做法**
```c
void read_input(int age) {
    if (age <= 0) {
        fprintf(stderr, "Invalid age!\n");
        exit(EXIT_FAILURE);
    }
}
```

---

## **5. `assert` vs. `static_assert`**
C11 标准引入了 `static_assert`，用于**编译时检查**：
```c
#include <assert.h>

static_assert(sizeof(int) == 4, "int 不是 4 字节");
```
- **`assert(expr)`**：运行时检查，影响性能，`NDEBUG` 可关闭。
- **`static_assert(expr, msg)`**：编译时检查，`NDEBUG` 不影响。

---

## **6. `assert` 在不同 C 标准中的支持**
| 标准版本 | `assert.h` 是否支持 |
|---------|----------------|
| C89/C90 | ✅ 支持 |
| C99     | ✅ 支持 |
| C11     | ✅ 支持（新增 `static_assert`） |
| C17     | ✅ 支持 |

---

## **总结**
| 关键点 | 说明 |
|--------|------|
| 作用 | 运行时检查表达式是否为真，否则终止程序 |
| 失败时 | 输出文件名、行号、表达式，调用 `abort()` |
| 预处理器控制 | `#define NDEBUG` 可禁用 `assert` |
| 适用场景 | 调试、检查内部假设 |
| 不适用场景 | 用户输入验证 |
| `static_assert` | C11 新增，编译期检查 |
