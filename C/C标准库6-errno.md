# C标准库6-errno

`errno.h` 是 C 语言标准库提供的头文件，主要用于**错误码机制**。它定义了一组错误码常量，以及**全局变量 `errno`**，用于指示最近一次函数调用失败的原因。

---

## **1. `errno.h` 的作用**
1. **提供 `errno` 变量**：
   - `errno` 记录了最近一次**库函数**调用的错误类型。
   - 主要用于系统调用（如 `open`、`read`、`write`）和标准库函数（如 `strtol`、`malloc`）。
   - `errno` 的值在**成功的函数调用时不会自动清零**，所以在检查错误前，应该手动清零。

2. **定义错误码**：
   - `errno.h` 预定义了一些错误码（如 `EACCES`、`ENOMEM`、`EINVAL`）。
   - 这些错误码都是**整数常量**，在不同的系统实现中可能有所不同。

---

## **2. `errno` 变量**
### **2.1 `errno` 变量的声明**
在 `<errno.h>` 头文件中，`errno` 的声明如下：
```c
extern int errno;
```
- 这是一个**全局变量**，但在多线程环境下，`errno` 是**线程局部存储（Thread-Local Storage, TLS）**，不同线程有独立的 `errno` 值。
- 需要包含 `<errno.h>` 头文件才能使用。

### **2.2 `errno` 的工作机制**
- 当标准库或系统调用失败时，它们会设置 `errno` 以表明失败的原因。
- `errno` 仅在**出错时**才会被修改，成功的调用不会改变 `errno`。
- **重要：`errno` 不会自动清零**，所以在检查错误前，建议先手动将其设为 `0`。

---

## **3. `errno` 使用示例**
### **3.1 检查 `errno`**
```c
#include <stdio.h>
#include <errno.h>
#include <string.h>

int main() {
    FILE *fp = fopen("non_existent_file.txt", "r");
    if (!fp) {
        printf("打开文件失败，错误码: %d\n", errno);
        printf("错误信息: %s\n", strerror(errno));
    }
    return 0;
}
```
#### **示例输出**
```
打开文件失败，错误码: 2
错误信息: No such file or directory
```
- `errno == 2` 代表 `ENOENT`（文件不存在）。
- `strerror(errno)` 返回相应的错误描述。

---

### **3.2 手动清零 `errno`**
```c
#include <stdio.h>
#include <errno.h>

int main() {
    errno = 0;  // 清除上次错误

    int result = remove("non_existent_file.txt");
    if (result == -1) {
        printf("删除文件失败，错误码: %d\n", errno);
    }
    return 0;
}
```
**为何要手动清零 `errno`？**
- 因为有些函数可能**不设置 `errno`**，导致误判错误原因。

---

## **4. 常见的 `errno` 错误码**
错误码在 `<errno.h>` 中以 `E` 开头，以下是 POSIX 兼容系统（如 Linux、macOS）中的常见错误码：

| 错误码         | 值  | 含义 |
|---------------|----|-------------------------|
| `EPERM`       | 1  | 操作不允许（无权限） |
| `ENOENT`      | 2  | 文件或目录不存在 |
| `ESRCH`       | 3  | 进程不存在 |
| `EINTR`       | 4  | 系统调用被信号中断 |
| `EIO`         | 5  | I/O 错误 |
| `ENXIO`       | 6  | 设备不存在或不可用 |
| `E2BIG`       | 7  | 参数列表过长 |
| `ENOMEM`      | 12 | 内存不足 |
| `EACCES`      | 13 | 权限不足 |
| `EFAULT`      | 14 | 无效地址 |
| `EBUSY`       | 16 | 设备或资源忙 |
| `EEXIST`      | 17 | 文件已存在 |
| `ENODEV`      | 19 | 设备不存在 |
| `EINVAL`      | 22 | 无效参数 |
| `EPIPE`       | 32 | 管道破裂（写入一个已关闭的管道） |
| `EDOM`        | 33 | 数学参数超出定义域（如 `sqrt(-1)`) |
| `ERANGE`      | 34 | 数值超出范围（如 `strtol` 溢出） |

#### **示例：用 `perror()` 显示错误**
```c
#include <stdio.h>
#include <errno.h>

int main() {
    FILE *fp = fopen("non_existent.txt", "r");
    if (!fp) {
        perror("文件打开失败");
    }
    return 0;
}
```
**示例输出**
```
文件打开失败: No such file or directory
```
- `perror()` 自动打印 `errno` 对应的错误信息。

---

## **5. `errno` 在多线程环境下**
在多线程程序中，每个线程应该有**独立的 `errno` 变量**，否则会导致线程间的错误信息互相干扰。

在**POSIX 线程（`pthread`）环境**下：
- `errno` 是**线程局部存储（TLS, Thread Local Storage）**，每个线程有自己的 `errno`，互不干扰。

### **示例：多线程 `errno`**
```c
#include <stdio.h>
#include <pthread.h>
#include <errno.h>
#include <unistd.h>

void *thread_func(void *arg) {
    errno = 0;
    int res = remove("non_existent_file.txt");
    if (res == -1) {
        printf("线程 %ld: 错误码 %d, %s\n", pthread_self(), errno, strerror(errno));
    }
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread_func, NULL);
    pthread_create(&t2, NULL, thread_func, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    return 0;
}
```
- 每个线程都有**独立的 `errno`**，不会互相干扰。

---

## **6. `errno` vs `perror()` vs `strerror()`**
| 方法        | 作用 |
|------------|-------------------------------|
| `errno`    | 存储错误码（整数值）。 |
| `perror()` | 打印错误信息（带 `errno` 对应的字符串）。 |
| `strerror(errno)` | 返回 `errno` 对应的错误描述字符串。 |

---


## **8. 总结**
- `errno.h` 定义了 `errno` 变量和标准错误码。
- `errno` 仅在**系统调用或库函数失败时**被设置，不会自动清零。
- 使用 `perror()` 或 `strerror(errno)` 获取错误信息。
- `errno` 在多线程环境下是**线程局部存储**，不同线程的 `errno` 互不干扰。
- 在 Windows 上，`errno` 需要使用 `_get_errno()` 访问。

> **建议：** 在错误检查前手动 `errno = 0;` 避免误判。
