# C标准库15-setjmp

`setjmp.h` 是 C 标准库中的头文件，**主要用于实现非本地跳转（long jump）**。它提供了一种在 C 语言中进行**异常处理**或**控制流跳转**的方法，类似于 `goto`，但可以跨函数跳转。

---

## **1. `setjmp.h` 的作用**
通常，C 语言的函数调用是**严格的栈结构**，当一个函数返回时，它的局部变量、调用栈等都会被销毁。但是，有时我们希望在**发生错误或特定情况时，立即跳回到某个状态，而不必通过普通的函数调用返回路径**。

### **`setjmp.h` 主要用于：**
- **错误处理**（如 C 语言中的 `try-catch` 机制）
- **跳出深层嵌套的函数调用**
- **恢复程序状态（如游戏状态回滚）**

---

## **2. `setjmp.h` 提供的核心函数**
### **2.1 `setjmp()`**
```c
int setjmp(jmp_buf env);
```
- **作用**：保存当前 CPU 的寄存器状态、堆栈指针、程序计数器（PC）等，以便稍后用 `longjmp()` 跳回这个位置。
- **参数**：
  - `env`：类型为 `jmp_buf`，用于存储当前的程序状态（上下文）。
- **返回值**：
  - **第一次调用时，返回 `0`**。
  - **当 `longjmp()` 跳回来时，返回 `longjmp()` 传递的 `val` 值**（通常不为 `0`）。

---

### **2.2 `longjmp()`**
```c
void longjmp(jmp_buf env, int val);
```
- **作用**：恢复 `setjmp()` 保存的状态，并**跳回 `setjmp()` 调用的位置**，就像 `setjmp()` 刚刚返回一样。
- **参数**：
  - `env`：要恢复的 `jmp_buf` 状态（必须是之前 `setjmp()` 保存的）。
  - `val`：返回值，**如果 `val == 0`，则 `longjmp()` 会强制返回 `1`，以区分 `setjmp()` 的第一次返回**。
- **返回值**：
  - **不会返回**，而是直接跳回 `setjmp()` 并返回 `val`。

---

## **3. `setjmp()` 和 `longjmp()` 的配合使用**
**示例：错误恢复**
```c
#include <stdio.h>
#include <setjmp.h>

jmp_buf env;  // 用于存储状态

void error_occurred() {
    printf("发生错误，回到安全点\n");
    longjmp(env, 1);  // 跳回 setjmp() 位置
}

int main() {
    if (setjmp(env) == 0) {
        printf("程序开始运行\n");
        error_occurred();  // 触发错误
        printf("这行不会被执行\n");
    } else {
        printf("从 error_occurred() 跳回来了！\n");
    }
    return 0;
}
```
### **运行结果**
```
程序开始运行
发生错误，回到安全点
从 error_occurred() 跳回来了！
```
---
## **4. `setjmp.h` 的工作原理**
- `setjmp()` **保存当前寄存器状态、栈指针、程序计数器** 等。
- `longjmp()` **恢复 `setjmp()` 保存的状态**，让程序继续执行，就像 `setjmp()` 立即返回了一样。

**简化流程**
1. `setjmp(env)` **保存当前程序状态**。
2. 代码继续执行，遇到 `longjmp(env, val)` 。
3. `longjmp()` **恢复 `env` 的状态**，让 `setjmp(env)` 直接返回 `val`（不为 0）。
4. `setjmp(env)` 处的代码继续执行，跳过 `longjmp()` 之后的代码。

---

## **5. `setjmp()` 的应用场景**
### **5.1 处理错误**
在某些情况下，我们可能希望**在某个位置检测到错误时，立即返回安全点**，而不是进行一层层的返回。

**示例：文件处理错误恢复**
```c
#include <stdio.h>
#include <setjmp.h>

jmp_buf env;

void process_file(const char *filename) {
    FILE *file = fopen(filename, "r");
    if (!file) {
        printf("打开文件失败！\n");
        longjmp(env, 1);
    }
    printf("成功打开文件：%s\n", filename);
    fclose(file);
}

int main() {
    if (setjmp(env) == 0) {
        process_file("不存在的文件.txt");
        printf("文件处理完成\n");
    } else {
        printf("从 process_file() 发生错误返回！\n");
    }
    return 0;
}
```
### **运行结果**
```
打开文件失败！
从 process_file() 发生错误返回！
```
---

### **5.2 退出深层递归**
当递归过深或发生异常时，**可以直接跳出递归，而不需要逐层返回**。

**示例：异常处理**
```c
#include <stdio.h>
#include <setjmp.h>

jmp_buf env;

void deep_function(int level) {
    printf("进入递归层级：%d\n", level);
    if (level > 5) {
        printf("递归太深，跳回主程序！\n");
        longjmp(env, 1);
    }
    deep_function(level + 1);
}

int main() {
    if (setjmp(env) == 0) {
        deep_function(1);
    } else {
        printf("递归已终止，回到主程序！\n");
    }
    return 0;
}
```
### **运行结果**
```
进入递归层级：1
进入递归层级：2
进入递归层级：3
进入递归层级：4
进入递归层级：5
进入递归层级：6
递归太深，跳回主程序！
递归已终止，回到主程序！
```
---

## **6. `setjmp()` 的注意事项**
1. **`setjmp()` 必须在 `longjmp()` 之前调用，否则行为未定义**。
2. **`longjmp()` 不能跳出 `main()`**，否则可能导致内存泄漏或程序崩溃。
3. **不能在 `setjmp()` 之后修改局部变量**，因为 `longjmp()` 不会恢复它们的值。
4. **避免使用 `setjmp()` 进行普通流程控制**，它主要用于错误处理。
5. **尽量避免与多线程结合使用**，因为 `setjmp()` 只保存当前线程的状态，多线程环境中会有问题。

---

## **7. `setjmp()` vs `goto`**
| 特性 | `setjmp()` + `longjmp()` | `goto` |
|------|-------------------|------|
| 跳转范围 | 可以跨函数跳转 | 只能在同一函数内 |
| 灵活性 | 可以恢复程序状态 | 仅能控制流 |
| 用途 | 异常处理、错误恢复 | 代码结构优化 |
| 开销 | 较高（存储寄存器、堆栈） | 较低（仅改变 PC 计数器） |

---

## **8. 总结**
- **`setjmp()`**：保存当前 CPU 寄存器状态和程序计数器（PC）。
- **`longjmp()`**：恢复 `setjmp()` 的状态，实现**跨函数跳转**。
- **主要用于异常处理、错误恢复、深度递归终止**。
- **不要用于普通流程控制，否则可能导致代码混乱！**
- **适用于 C 语言，没有 `try-catch` 的情况下提供类似功能**。

---