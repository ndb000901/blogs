# C标准库7-ctype

`ctype.h` 是 C 语言标准库的头文件，提供了一系列用于**字符分类和转换**的函数，主要用于处理 `char` 和 `int` 类型的字符输入。这些函数主要用于**检查字符类型**（如字母、数字、空白字符等）和**转换字符大小写**。

---

## **1. `ctype.h` 的作用**
`ctype.h` 主要用于：
1. **检查字符类型**（如 `isalpha()` 判断是否为字母）。
2. **转换字符大小写**（如 `toupper()` 将小写字母转换为大写）。
3. **保证字符处理的可移植性**（在不同系统或字符集环境下保持一致）。

---

## **2. `ctype.h` 常用函数**
所有 `ctype.h` 函数都接收 `int` 类型的参数，并返回 `int` 作为结果：
- **输入参数**：通常是 `unsigned char`（即 0-255 之间的 ASCII 值）。
- **返回值**：一般为布尔值（0 表示 `false`，非 0 表示 `true`），或转换后的字符值。

### **2.1 字符检查函数**
| 函数 | 作用 | 说明 |
|------|------|------|
| `isalpha(c)` | 是否为字母 | 等同于 `isupper(c) || islower(c)` |
| `isalnum(c)` | 是否为字母或数字 | `isalpha(c) || isdigit(c)` |
| `isdigit(c)` | 是否为数字（0-9） | ASCII 码 `48-57` (`'0'`-`'9'`) |
| `isxdigit(c)` | 是否为十六进制数字 | `0-9, A-F, a-f` |
| `islower(c)` | 是否为小写字母 | ASCII 码 `97-122` (`'a'`-`'z'`) |
| `isupper(c)` | 是否为大写字母 | ASCII 码 `65-90` (`'A'`-`'Z'`) |
| `isspace(c)` | 是否为空白字符 | `'\t' '\n' '\v' '\f' '\r' ' '` |
| `isblank(c)` | 是否为空格或制表符 (`' '` 和 `'\t'`) | C99 添加 |
| `iscntrl(c)` | 是否为控制字符 | ASCII 码 `0-31` 和 `127`（如 `\n` `\t` `\r`） |
| `isgraph(c)` | 是否为可打印字符（除空格） | `isprint(c) && !isspace(c)` |
| `isprint(c)` | 是否为可打印字符 | 任何可见字符（包括空格） |
| `ispunct(c)` | 是否为标点符号 | 既不是字母、数字，也不是空白字符 |
| `isascii(c)` | 是否为 ASCII 字符 | **非标准函数**，检查 `c <= 127` |

#### **示例：使用字符检查函数**
```c
#include <stdio.h>
#include <ctype.h>

int main() {
    char c = 'A';
    
    if (isalpha(c))
        printf("%c 是字母\n", c);
    
    if (isdigit('8'))
        printf("'8' 是数字\n");

    if (isspace(' '))
        printf("空格是空白字符\n");

    return 0;
}
```
**输出：**
```
A 是字母
'8' 是数字
空格是空白字符
```

---

### **2.2 字符转换函数**
| 函数 | 作用 | 说明 |
|------|------|------|
| `tolower(c)` | 转换为小写 | 仅当 `c` 为大写字母时生效 |
| `toupper(c)` | 转换为大写 | 仅当 `c` 为小写字母时生效 |

#### **示例：使用 `toupper()` 和 `tolower()`**
```c
#include <stdio.h>
#include <ctype.h>

int main() {
    char c1 = 'a';
    char c2 = 'Z';

    printf("%c 转换为大写: %c\n", c1, toupper(c1));
    printf("%c 转换为小写: %c\n", c2, tolower(c2));

    return 0;
}
```
**输出：**
```
a 转换为大写: A
Z 转换为小写: z
```

---

## **3. `ctype.h` 函数的实现原理**
### **3.1 `isalpha()` 的实现原理**
`isalpha(c)` 其实等价于：
```c
#define isalpha(c) (('a' <= (c) && (c) <= 'z') || ('A' <= (c) && (c) <= 'Z'))
```
类似地，`isdigit(c)` 也是基于 ASCII 范围：
```c
#define isdigit(c) ('0' <= (c) && (c) <= '9')
```

### **3.2 `toupper()` 和 `tolower()` 的实现**
```c
#define toupper(c) ((c) >= 'a' && (c) <= 'z' ? (c) - 32 : (c))
#define tolower(c) ((c) >= 'A' && (c) <= 'Z' ? (c) + 32 : (c))
```
- ASCII 码中：
  - `'A'` 到 `'Z'` 的范围是 `65-90`
  - `'a'` 到 `'z'` 的范围是 `97-122`
  - `小写字母 = 大写字母 + 32`
  - `大写字母 = 小写字母 - 32`

---

## **4. `ctype.h` 在不同字符集下的行为**
在不同字符集（如 UTF-8、GBK）下，`ctype.h` 的行为可能有所不同：
- 在 **纯 ASCII** 环境下，`ctype.h` 工作正常。
- 在 **UTF-8** 多字节字符环境下，`isalpha()` 等函数可能无法正确识别非 ASCII 字符（如 `é`）。
- **解决方案**：使用 **`wctype.h`** 处理宽字符。

### **宽字符处理**
```c
#include <wctype.h>
#include <wchar.h>

int main() {
    wchar_t wc = L'你';
    if (iswalpha(wc)) {
        wprintf(L"%lc 是字母\n", wc);
    }
    return 0;
}
```
- `iswalpha()` 适用于宽字符（Unicode 字符）。

---

## **5. `ctype.h` 的注意事项**
1. **参数类型**
   - `ctype.h` 的所有函数都接收 `int`，但**只对 `unsigned char`（0-255）或 `EOF` 有效**。
   - 如果传入**负数（例如 `char` 被默认解释为 `signed char`）**，可能会产生**未定义行为**。

2. **检查前先转换 `unsigned char`**
   ```c
   int c = (unsigned char)ch;  // 避免未定义行为
   ```

3. **不能用于 Unicode**
   - `ctype.h` 仅适用于**单字节字符集（如 ASCII、ISO-8859-1）**，不支持多字节字符集（如 UTF-8）。
   - 处理 Unicode 字符应使用 **`wctype.h`** 或 **ICU 库**。

---

## **6. 总结**
- `ctype.h` 提供字符分类（`isalpha()`、`isdigit()`）和转换（`toupper()`、`tolower()`）函数。
- 适用于 ASCII 字符，但不支持 Unicode。
- **参数需转换为 `unsigned char` 避免未定义行为**。
- **在宽字符环境（Unicode）下，推荐使用 `wctype.h`**。
