# C标准库13-stdarg

### **`<stdarg.h>`** 的功能概述

`<stdarg.h>` 是 C 标准库提供的一个头文件，用于处理不定参数函数。

---

### **核心概念：不定参数函数**
不定参数函数是指参数数量或类型不固定的函数。例如：
```c
int printf(const char *format, ...);
```
其中 `...` 表示该函数可以接收变长参数。

---

### **`<stdarg.h>` 提供的类型和宏**
`<stdarg.h>` 提供以下工具来处理不定参数：

1. **`va_list`**  
   - 类型，用于声明存储变参信息的对象。
   - 用法：作为一个句柄，用于访问可变参数列表。

2. **`va_start(va_list ap, last)`**  
   - 宏，用于初始化 `va_list` 对象。
   - 参数：
     - `ap`: 要初始化的 `va_list` 对象。
     - `last`: 变参列表中，最后一个固定参数的名称。
   - 功能：设置访问不定参数的起点。

3. **`va_arg(va_list ap, type)`**  
   - 宏，用于从参数列表中检索下一个参数的值。
   - 参数：
     - `ap`: 正在访问的 `va_list` 对象。
     - `type`: 要提取的参数的类型。
   - 返回值：指定类型的参数值。

4. **`va_copy(va_list dest, va_list src)`** (C99 引入)
   - 宏，用于复制一个 `va_list`。
   - 参数：
     - `dest`: 目标 `va_list`。
     - `src`: 源 `va_list`。

5. **`va_end(va_list ap)`**  
   - 宏，用于清理 `va_list` 对象。
   - 参数：
     - `ap`: 要清理的 `va_list` 对象。
   - 功能：结束不定参数访问，避免未定义行为。

---

### **示例：实现一个简单的变参函数**
下面是一个简单的示例，展示如何使用 `<stdarg.h>` 处理不定参数：

#### 示例 1：计算任意数量整数的和
```c
#include <stdarg.h>
#include <stdio.h>

int sum(int count, ...) {
    int total = 0;

    va_list args;            // 声明 va_list 对象
    va_start(args, count);   // 初始化 args，指向第一个变参

    for (int i = 0; i < count; i++) {
        total += va_arg(args, int);  // 依次获取变参
    }

    va_end(args);            // 清理 va_list 对象

    return total;
}

int main() {
    printf("Sum of 1, 2, 3: %d\n", sum(3, 1, 2, 3));
    printf("Sum of 10, 20, 30, 40: %d\n", sum(4, 10, 20, 30, 40));
    return 0;
}
```

**输出：**
```
Sum of 1, 2, 3: 6
Sum of 10, 20, 30, 40: 100
```

---

#### 示例 2：实现一个简化版的 `printf`
```c
#include <stdarg.h>
#include <stdio.h>

void my_printf(const char *format, ...) {
    va_list args;
    va_start(args, format);

    const char *p = format;
    while (*p) {
        if (*p == '%' && *(p + 1) == 'd') {  // 简单支持 %d
            int num = va_arg(args, int);
            printf("%d", num);
            p++;
        } else {
            putchar(*p);
        }
        p++;
    }

    va_end(args);
}

int main() {
    my_printf("Hello %d World %d!\n", 42, 2024);
    return 0;
}
```

**输出：**
```
Hello 42 World 2024!
```

---

### **注意事项**
1. **必须匹配参数类型：**  
   `va_arg` 中的类型 `type` 必须与传入参数类型匹配，否则可能引发未定义行为。例如：
   ```c
   int x = va_arg(ap, double); // 错误：实际参数是 double，但要求 int。
   ```

2. **`va_start` 和 `va_end` 的成对使用：**  
   每次使用 `va_start` 初始化 `va_list` 后，必须调用 `va_end` 进行清理。

3. **不可预测的类型和顺序：**  
   不定参数没有类型检查，开发者必须自行约定参数的数量和类型。例如，通过一个固定参数（如 `printf` 的 `format`）提供线索。

---

### **应用场景**
1. **格式化输出：** `printf` 和 `fprintf`。
2. **通用函数：** 如日志记录器，接受多种类型的参数。
3. **工具类函数：** 如接受可变数量输入的数学计算函数。
