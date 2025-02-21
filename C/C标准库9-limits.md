# C标准库9-limits

`limits.h` 是 C 语言标准库的一个头文件，**定义了各种基本数据类型的范围限制**，包括 `char`、`int`、`long`、`short` 等整数类型的**最小值和最大值**。

在不同的计算机架构（如 32 位、64 位）和编译器实现中，整数类型的取值范围可能不同，因此 `limits.h` 提供了可移植的方式来查询这些限制。

---

## **1. `limits.h` 的作用**
- **提供各种整数类型的取值范围**，如：
  - `CHAR_MIN` / `CHAR_MAX`
  - `INT_MIN` / `INT_MAX`
  - `LONG_MIN` / `LONG_MAX`
- **避免硬编码**，保证代码的可移植性。
- **用于动态分配数组大小时，防止越界错误**。

---

## **2. `limits.h` 定义的常量**
`limits.h` 主要定义了**整型类型**的范围，具体如下：

### **2.1 `char` 类型的限制**
| 宏常量 | 说明 |
|--------|------|
| `CHAR_BIT` | `char` 类型的位数（通常为 8） |
| `CHAR_MIN` | `char` 类型的最小值（`signed char` 时为 `-128`，`unsigned char` 时为 `0`） |
| `CHAR_MAX` | `char` 类型的最大值（`signed char` 时为 `127`，`unsigned char` 时为 `255`） |

在某些架构上，`char` 可能是 **signed** 或 **unsigned**：
- `SCHAR_MIN` / `SCHAR_MAX` 适用于 `signed char`
- `UCHAR_MAX` 适用于 `unsigned char`

### **2.2 `short` 类型的限制**
| 宏常量 | 说明 |
|--------|------|
| `SHRT_MIN` | `short` 类型的最小值（通常为 `-32768`） |
| `SHRT_MAX` | `short` 类型的最大值（通常为 `32767`） |
| `USHRT_MAX` | `unsigned short` 类型的最大值（通常为 `65535`） |

### **2.3 `int` 类型的限制**
| 宏常量 | 说明 |
|--------|------|
| `INT_MIN` | `int` 类型的最小值（通常为 `-2147483648`） |
| `INT_MAX` | `int` 类型的最大值（通常为 `2147483647`） |
| `UINT_MAX` | `unsigned int` 类型的最大值（通常为 `4294967295`） |

### **2.4 `long` 类型的限制**
| 宏常量 | 说明 |
|--------|------|
| `LONG_MIN` | `long` 类型的最小值（通常为 `-9223372036854775808`） |
| `LONG_MAX` | `long` 类型的最大值（通常为 `9223372036854775807`） |
| `ULONG_MAX` | `unsigned long` 类型的最大值（通常为 `18446744073709551615`） |

### **2.5 `long long` 类型的限制（C99 引入）**
| 宏常量 | 说明 |
|--------|------|
| `LLONG_MIN` | `long long` 类型的最小值（通常为 `-9223372036854775808`） |
| `LLONG_MAX` | `long long` 类型的最大值（通常为 `9223372036854775807`） |
| `ULLONG_MAX` | `unsigned long long` 类型的最大值（通常为 `18446744073709551615`） |

---

## **3. `limits.h` 的使用示例**
```c
#include <stdio.h>
#include <limits.h>

int main() {
    printf("char 类型:\n");
    printf("  CHAR_BIT  = %d\n", CHAR_BIT);
    printf("  CHAR_MIN  = %d\n", CHAR_MIN);
    printf("  CHAR_MAX  = %d\n", CHAR_MAX);

    printf("\nshort 类型:\n");
    printf("  SHRT_MIN  = %d\n", SHRT_MIN);
    printf("  SHRT_MAX  = %d\n", SHRT_MAX);
    printf("  USHRT_MAX = %u\n", USHRT_MAX);

    printf("\nint 类型:\n");
    printf("  INT_MIN   = %d\n", INT_MIN);
    printf("  INT_MAX   = %d\n", INT_MAX);
    printf("  UINT_MAX  = %u\n", UINT_MAX);

    printf("\nlong 类型:\n");
    printf("  LONG_MIN  = %ld\n", LONG_MIN);
    printf("  LONG_MAX  = %ld\n", LONG_MAX);
    printf("  ULONG_MAX = %lu\n", ULONG_MAX);

    printf("\nlong long 类型:\n");
    printf("  LLONG_MIN  = %lld\n", LLONG_MIN);
    printf("  LLONG_MAX  = %lld\n", LLONG_MAX);
    printf("  ULLONG_MAX = %llu\n", ULLONG_MAX);

    return 0;
}
```
**示例输出（64 位 Linux）：**
```
char 类型:
  CHAR_BIT  = 8
  CHAR_MIN  = -128
  CHAR_MAX  = 127

short 类型:
  SHRT_MIN  = -32768
  SHRT_MAX  = 32767
  USHRT_MAX = 65535

int 类型:
  INT_MIN   = -2147483648
  INT_MAX   = 2147483647
  UINT_MAX  = 4294967295

long 类型:
  LONG_MIN  = -9223372036854775808
  LONG_MAX  = 9223372036854775807
  ULONG_MAX = 18446744073709551615

long long 类型:
  LLONG_MIN  = -9223372036854775808
  LLONG_MAX  = 9223372036854775807
  ULLONG_MAX = 18446744073709551615
```

---

## **4. `limits.h` 的实现原理**
不同系统的 `limits.h` 可能略有不同，但通常这些值是**宏定义**，具体值由编译器或处理器架构决定。例如：

```c
#define CHAR_BIT 8
#define SCHAR_MIN (-128)
#define SCHAR_MAX 127
#define UCHAR_MAX 255

#define SHRT_MIN (-32768)
#define SHRT_MAX 32767
#define USHRT_MAX 65535

#define INT_MIN (-2147483648)
#define INT_MAX 2147483647
#define UINT_MAX 4294967295U

#define LONG_MIN (-9223372036854775808L)
#define LONG_MAX 9223372036854775807L
#define ULONG_MAX 18446744073709551615UL
```
这些值的大小取决于 **CPU 架构和编译器**。

---

## **5. `limits.h` 的应用场景**
### **5.1 防止溢出**
```c
#include <stdio.h>
#include <limits.h>

int main() {
    int a = INT_MAX;
    int b = 1;

    if (a > INT_MAX - b) {
        printf("加法溢出，无法执行 a + b\n");
    } else {
        printf("a + b = %d\n", a + b);
    }

    return 0;
}
```
**输出：**
```
加法溢出，无法执行 a + b
```
---

## **6. `limits.h` 的注意事项**
1. **跨平台适用**
   - `limits.h` 的值可能因平台不同而有所变化，因此不要**硬编码**数值，而是使用 `limits.h` 。
  
2. **适用于整数类型，不包含浮点类型**
   - 浮点数的限制在 `float.h` 里（如 `FLT_MAX`、`DBL_MAX`）。
  
3. **避免整数溢出**
   - 在算术计算时，应检查是否超出 `limits.h` 提供的范围。

---

## **7. 总结**
- `limits.h` 定义了 C 语言整数类型的**最小值和最大值**，提高代码的**可移植性**。
- 常见宏包括 `CHAR_MIN`、`INT_MAX`、`LONG_MAX` 等。
- 主要用于**防止溢出**、**跨平台兼容性**和**动态内存分配**等场景。

