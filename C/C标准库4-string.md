# C标准库4-string

`string.h` 头文件提供了**字符串处理**和**内存操作**相关的函数，涵盖字符串复制、比较、查找、拼接，以及内存块操作等功能。

---

## **1. `string.h` 提供的数据类型**
| 数据类型 | 说明 |
|---------|------|
| `size_t` | 表示对象大小的无符号整数类型，通常用于字符串长度和内存大小计算。 |
| `NULL` | 空指针常量，通常用于表示字符串结束或无效指针。 |

---

## **2. `string.h` 主要函数**
### **（1）字符串操作函数**
#### **`strlen()` 计算字符串长度**
```c
size_t strlen(const char *str);
```
返回字符串 `str` 的长度（不包括 `\0` 终止符）。

```c
#include <stdio.h>
#include <string.h>

int main() {
    char str[] = "Hello, world!";
    printf("字符串长度: %lu\n", strlen(str));  // 输出: 13
    return 0;
}
```

---

#### **`strcpy()` 复制字符串**
```c
char *strcpy(char *dest, const char *src);
```
将 `src` 复制到 `dest`，包括 `\0` 终止符。**注意：目标缓冲区必须足够大，否则会导致溢出！**

```c
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "Hello";
    char dest[10];  // 目标数组足够大
    strcpy(dest, src);
    printf("复制后: %s\n", dest);
    return 0;
}
```

**⚠️`strcpy()` 的问题：**
- **不安全**，如果 `dest` 空间不够，可能导致缓冲区溢出！
- **推荐使用 `strncpy()` 以限制复制长度。**

---

#### **`strncpy()` 复制指定长度的字符串**
```c
char *strncpy(char *dest, const char *src, size_t n);
```
最多复制 `n` 个字符到 `dest`，若 `src` 长度小于 `n`，则剩余部分用 `\0` 填充。

```c
char dest[10];
strncpy(dest, "Hello", 10);
```

**⚠️ 注意：**
- 如果 `src` 长度超过 `n`，则不会自动添加 `\0`，需要手动处理：
  ```c
  dest[n-1] = '\0';
  ```

---

#### **`strcat()` 拼接字符串**
```c
char *strcat(char *dest, const char *src);
```
将 `src` 追加到 `dest` 末尾（`dest` 必须足够大），并自动添加 `\0`。

```c
#include <stdio.h>
#include <string.h>

int main() {
    char dest[20] = "Hello";
    strcat(dest, ", world!");
    printf("%s\n", dest);  // 输出: Hello, world!
    return 0;
}
```

---

#### **`strncat()` 限制拼接长度**
```c
char *strncat(char *dest, const char *src, size_t n);
```
最多拼接 `n` 个字符，`dest` 必须足够大，否则可能导致溢出。

```c
char dest[20] = "Hello";
strncat(dest, ", world!", 5);  // 只拼接5个字符
```

---

### **（2）字符串比较**
#### **`strcmp()` 比较两个字符串**
```c
int strcmp(const char *s1, const char *s2);
```
- `strcmp(s1, s2) == 0`：`s1` 和 `s2` 相等。
- `strcmp(s1, s2) > 0`：`s1` 大于 `s2`。
- `strcmp(s1, s2) < 0`：`s1` 小于 `s2`。

```c
#include <stdio.h>
#include <string.h>

int main() {
    char str1[] = "apple";
    char str2[] = "banana";
    if (strcmp(str1, str2) < 0) {
        printf("apple < banana\n");
    }
    return 0;
}
```

---

#### **`strncmp()` 限制比较长度**
```c
int strncmp(const char *s1, const char *s2, size_t n);
```
最多比较 `n` 个字符。

```c
strncmp("abc", "abd", 2);  // 只比较 "ab"，返回 0（相等）
```

---

### **（3）字符串查找**
#### **`strchr()` 查找字符**
```c
char *strchr(const char *str, int c);
```
返回 `str` 中 `c` 第一次出现的位置指针，若未找到则返回 `NULL`。

```c
char *p = strchr("hello", 'l');  // 返回 "llo"
```

---

#### **`strrchr()` 查找最后一次出现的字符**
```c
char *strrchr(const char *str, int c);
```
返回 `str` 中 `c` **最后一次** 出现的位置指针。

---

#### **`strstr()` 查找子串**
```c
char *strstr(const char *haystack, const char *needle);
```
返回 `needle` 在 `haystack` 中第一次出现的位置指针。

```c
char *p = strstr("hello world", "world");  // 返回 "world"
```

---

### **（4）字符串分割**
#### **`strtok()` 拆分字符串**
```c
char *strtok(char *str, const char *delim);
```
- `str` 为待分割字符串，`delim` 为分隔符。
- 第一次调用传入 `str`，后续调用传入 `NULL`。

```c
#include <stdio.h>
#include <string.h>

int main() {
    char str[] = "apple,banana,grape";
    char *token = strtok(str, ",");
    while (token) {
        printf("%s\n", token);
        token = strtok(NULL, ",");
    }
    return 0;
}
```

---

### **（5）内存操作**
#### **`memcpy()` 复制内存**
```c
void *memcpy(void *dest, const void *src, size_t n);
```
用于**拷贝二进制数据**，不会自动添加 `\0`。

```c
char src[] = "Hello";
char dest[10];
memcpy(dest, src, 6);  // 包括 '\0'
```

---

#### **`memmove()` 处理重叠拷贝**
```c
void *memmove(void *dest, const void *src, size_t n);
```
`memcpy()` 不支持**重叠区域**拷贝，而 `memmove()` 可以。

```c
char str[] = "abcdef";
memmove(str + 2, str, 4);  // 变为 "ababcf"
```

---

#### **`memset()` 内存初始化**
```c
void *memset(void *s, int c, size_t n);
```
用于填充内存块：

```c
char buffer[10];
memset(buffer, 0, sizeof(buffer));  // 将 buffer 置为 0
```

---

## **3. `string.h` 使用注意**
1. **`strcpy()`、`strcat()` 存在溢出风险**，推荐 `strncpy()`、`strncat()`。
2. **`memcpy()` 不能处理重叠区域**，应使用 `memmove()`。
3. **`strtok()` 是不可重入的**，多线程环境推荐使用 `strtok_r()`（POSIX）。

---

## **4. 总结**
- **字符串操作**：`strlen()`、`strcpy()`、`strncpy()`、`strcat()`、`strncat()`。
- **字符串比较**：`strcmp()`、`strncmp()`。
- **查找**：`strchr()`、`strrchr()`、`strstr()`。
- **分割**：`strtok()`。
- **内存操作**：`memcpy()`、`memmove()`、`memset()`。
