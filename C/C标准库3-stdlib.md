# C标准库3-stdlib

`stdlib.h`（**Standard Library**）是 C 标准库中最重要的头文件之一，提供了**动态内存管理**、**程序控制**、**转换**、**排序/搜索**等常用函数。所有 C 语言程序几乎都会用到它。

---

## **1. `stdlib.h` 提供的类型**
| 类型名 | 说明 |
|--------|------|
| `size_t` | 无符号整数类型，表示对象大小，通常用于 `malloc()`、`strlen()` 等函数。 |
| `ptrdiff_t` | 有符号整数类型，表示指针间的差值。 |
| `div_t` | 用于 `div()` 整数除法的结构体，包含 `quot`（商）和 `rem`（余数）。 |
| `ldiv_t` | 类似 `div_t`，但用于 `long` 类型。 |
| `NULL` | 空指针常量，表示无效指针。 |
| `EXIT_SUCCESS` | `0`，表示程序正常结束。 |
| `EXIT_FAILURE` | `1`，表示程序异常终止。 |
| `RAND_MAX` | `rand()` 生成的最大随机数值。 |

---

## **2. `stdlib.h` 主要函数**

### **（1）动态内存管理**
#### **`malloc()` 分配内存**
```c
void *malloc(size_t size);
```
分配 `size` 字节的内存，返回指向分配空间的指针。**未初始化**，内容为随机值。

```c
int *arr = (int *)malloc(10 * sizeof(int));
if (!arr) {
    printf("内存分配失败\n");
    exit(EXIT_FAILURE);
}
```

---

#### **`calloc()` 分配并清零内存**
```c
void *calloc(size_t num, size_t size);
```
分配 `num * size` 字节的内存，并初始化为 `0`。

```c
int *arr = (int *)calloc(10, sizeof(int));  // 分配10个int，全部初始化为0
```

---

#### **`realloc()` 重新分配内存**
```c
void *realloc(void *ptr, size_t new_size);
```
扩展/缩小 `ptr` 指向的内存大小，并返回新的指针。

```c
arr = (int *)realloc(arr, 20 * sizeof(int));  // 扩展到20个int
```

---

#### **`free()` 释放内存**
```c
void free(void *ptr);
```
释放 `malloc()`/`calloc()`/`realloc()` 分配的内存，避免内存泄漏。

```c
free(arr);
```

---

### **（2）程序控制**
#### **`exit()` 终止程序**
```c
void exit(int status);
```
终止程序并返回状态码。通常：
- `exit(0)`（或 `EXIT_SUCCESS`）：正常结束
- `exit(1)`（或 `EXIT_FAILURE`）：异常结束

---

#### **`abort()` 强制终止**
```c
void abort(void);
```
立即终止程序，不执行 `atexit()` 注册的函数。

---

#### **`atexit()` 注册退出函数**
```c
int atexit(void (*func)(void));
```
注册一个**程序终止时执行**的函数（最多 32 个）。

```c
void goodbye() {
    printf("程序即将结束\n");
}

int main() {
    atexit(goodbye);
    printf("主程序运行中...\n");
    return 0;
}
```

---

### **（3）数值转换**
#### **`atoi()` 字符串转整数**
```c
int atoi(const char *str);
```
将字符串转换为 `int`，若转换失败返回 `0`。

```c
int num = atoi("1234");  // num = 1234
```

---

#### **`atof()` 字符串转浮点数**
```c
double atof(const char *str);
```
转换字符串为 `double` 类型。

```c
double d = atof("3.1415");  // d = 3.1415
```

---

#### **`strtol()` 和 `strtoll()` 转换长整型**
```c
long int strtol(const char *str, char **endptr, int base);
long long int strtoll(const char *str, char **endptr, int base);
```
支持**二进制、八进制、十进制、十六进制**等格式，并返回转换后字符串的**未转换部分**。

```c
char *end;
long val = strtol("101101", &end, 2);  // 解析二进制数，val = 45
```

---

### **（4）随机数**
#### **`rand()` 生成随机数**
```c
int rand(void);
```
返回 `0 ~ RAND_MAX` 之间的整数。

```c
int r = rand() % 100;  // 生成 0~99 之间的随机数
```

---

#### **`srand()` 设置随机种子**
```c
void srand(unsigned int seed);
```
常用于初始化随机数生成器，以保证每次运行随机数不同。

```c
srand(time(NULL));  // 以当前时间作为种子
int r = rand();
```

---

### **（5）排序与搜索**
#### **`qsort()` 快速排序**
```c
void qsort(void *base, size_t num, size_t size, int (*compar)(const void *, const void *));
```
适用于各种数据类型的快速排序。

```c
#include <stdio.h>
#include <stdlib.h>

int compare(const void *a, const void *b) {
    return *(int *)a - *(int *)b;
}

int main() {
    int arr[] = {5, 2, 9, 1, 3};
    int size = sizeof(arr) / sizeof(arr[0]);
    qsort(arr, size, sizeof(int), compare);
    for (int i = 0; i < size; i++) printf("%d ", arr[i]);
    return 0;
}
```

---

#### **`bsearch()` 二分查找**
```c
void *bsearch(const void *key, const void *base, size_t num, size_t size, int (*compar)(const void *, const void *));
```
在**已排序数组**中查找元素。

```c
int key = 3;
int *found = (int *)bsearch(&key, arr, size, sizeof(int), compare);
```

---

## **3. `stdlib.h` 重要注意事项**
1. **`malloc()` 分配的内存必须用 `free()` 释放，防止内存泄漏**。
2. **`rand()` 生成的是伪随机数，应使用 `srand(time(NULL))` 设置种子**。
3. **`qsort()` 适用于排序各种数据类型，但 `bsearch()` 仅适用于已排序数组**。
4. **`atoi()` 不能检测错误，推荐使用 `strtol()` 以获取错误信息**。

---

## **4. `stdlib.h` 速查表**
| 作用 | 函数 |
|------|------|
| **动态内存管理** | `malloc()`、`calloc()`、`realloc()`、`free()` |
| **程序控制** | `exit()`、`abort()`、`atexit()` |
| **转换** | `atoi()`、`atof()`、`strtol()`、`strtod()` |
| **随机数** | `rand()`、`srand()` |
| **排序/查找** | `qsort()`、`bsearch()` |
| **数学运算** | `abs()`、`labs()`、`div()`、`ldiv()` |

---
