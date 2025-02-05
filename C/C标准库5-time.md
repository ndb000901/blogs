# C标准库5-time

`time.h` 头文件用于提供日期和时间相关的函数和数据类型，支持**时间获取、时间计算、格式化输出**等操作，适用于 C 语言标准库中的时间管理。

---

## **1. `time.h` 提供的数据类型**
`time.h` 定义了几个关键的时间相关数据类型：

| 类型 | 描述 |
|------|------|
| `time_t` | 用于表示日历时间的整数类型，通常是**秒数**（从 Unix 纪元 `1970-01-01 00:00:00 UTC` 开始）。|
| `clock_t` | 用于表示**CPU 处理时间**，通常单位是**时钟周期数**，需除以 `CLOCKS_PER_SEC` 转换为秒。|
| `struct tm` | 结构体，表示**拆解后的时间**，包含年、月、日、时、分、秒等信息。|
| `size_t` | 用于存储对象的大小，通常用于时间格式化函数。|

---

## **2. `struct tm` 结构体**
`struct tm` 结构体用于存储分解后的时间信息，包含以下字段：

```c
struct tm {
    int tm_sec;   // 秒，取值 0-59（闰秒时可为 60）
    int tm_min;   // 分钟，取值 0-59
    int tm_hour;  // 小时，取值 0-23
    int tm_mday;  // 一个月中的日期，取值 1-31
    int tm_mon;   // 月份，取值 0-11（0=1月，11=12月）
    int tm_year;  // 从 1900 年起的年数，例如 2025 年表示为 125
    int tm_wday;  // 星期几，取值 0-6（0=星期日）
    int tm_yday;  // 一年中的第几天，取值 0-365
    int tm_isdst; // 夏令时标志（>0 表示使用夏令时，0 表示不使用，<0 由系统决定）
};
```

---

## **3. `time.h` 提供的主要函数**
### **（1）获取当前时间**
#### **`time_t time(time_t *t);`**
获取当前**Unix 时间戳**（自 1970-01-01 00:00:00 UTC 以来的秒数）。

```c
#include <stdio.h>
#include <time.h>

int main() {
    time_t now;
    time(&now);
    printf("当前时间戳: %ld\n", now);
    return 0;
}
```

---

### **（2）时间转换**
#### **`struct tm *localtime(const time_t *t);`**
将 `time_t` 转换为**本地时间（struct tm 格式）**。

```c
#include <stdio.h>
#include <time.h>

int main() {
    time_t now = time(NULL);
    struct tm *local = localtime(&now);
    printf("当前本地时间: %d-%02d-%02d %02d:%02d:%02d\n",
           local->tm_year + 1900, local->tm_mon + 1, local->tm_mday,
           local->tm_hour, local->tm_min, local->tm_sec);
    return 0;
}
```

#### **`struct tm *gmtime(const time_t *t);`**
将 `time_t` 转换为**UTC 时间（协调世界时）**。

```c
struct tm *gmtime(const time_t *t);
```

#### **`time_t mktime(struct tm *t);`**
将 `struct tm` 转换回 `time_t`，用于时间计算。

```c
time_t mktime(struct tm *t);
```

---

### **（3）格式化时间输出**
#### **`size_t strftime(char *s, size_t max, const char *format, const struct tm *tm);`**
用于格式化 `struct tm` 结构体的时间，转换为字符串。

```c
#include <stdio.h>
#include <time.h>

int main() {
    time_t now = time(NULL);
    struct tm *local = localtime(&now);
    char buffer[100];

    strftime(buffer, sizeof(buffer), "当前时间: %Y-%m-%d %H:%M:%S", local);
    printf("%s\n", buffer);

    return 0;
}
```

常见的格式化标志：
| 格式 | 说明 |
|------|------|
| `%Y` | 4 位年份（如 2025） |
| `%m` | 2 位月份（01-12） |
| `%d` | 2 位日期（01-31） |
| `%H` | 24 小时制小时（00-23） |
| `%M` | 分钟（00-59） |
| `%S` | 秒（00-59） |
| `%A` | 星期几（完整英文，如 "Monday"） |
| `%a` | 星期几（缩写，如 "Mon"） |
| `%B` | 月份（完整英文，如 "January"） |
| `%b` | 月份（缩写，如 "Jan"） |
| `%p` | AM/PM（仅用于 12 小时制） |

---

### **（4）计时函数**
#### **`clock_t clock(void);`**
返回**CPU 运行时间**（单位：时钟周期数），需要除以 `CLOCKS_PER_SEC` 计算秒数。

```c
#include <stdio.h>
#include <time.h>

int main() {
    clock_t start = clock();
    
    // 模拟任务
    for (volatile long i = 0; i < 100000000; i++);

    clock_t end = clock();
    double elapsed_time = (double)(end - start) / CLOCKS_PER_SEC;
    printf("代码运行时间: %.6f 秒\n", elapsed_time);

    return 0;
}
```

---

### **（5）时间间隔计算**
#### **`double difftime(time_t end, time_t start);`**
计算两个 `time_t` 时间戳之间的**秒数间隔**。

```c
#include <stdio.h>
#include <time.h>

int main() {
    time_t start = time(NULL);
    sleep(2); // 等待 2 秒
    time_t end = time(NULL);

    printf("时间差: %.f 秒\n", difftime(end, start));
    return 0;
}
```

---

## **4. `time.h` 宏定义**
| 宏 | 说明 |
|----|------|
| `CLOCKS_PER_SEC` | 每秒的时钟周期数，通常为 1000000（1 MHz） |
| `NULL` | 空指针 |
| `TIME_UTC` | 表示 UTC 时钟源的宏（C11 标准） |

---

## **5. `time.h` 使用注意事项**
1. `time_t` 的单位是 **秒**，但有些系统可能使用 **毫秒** 或 **其他时间单位**，需查看实现。
2. `localtime()` 和 `gmtime()` 返回的 `struct tm` 指针是**静态内存**，会被后续调用覆盖，应使用 `memcpy()` 复制。
3. `clock()` 计算的是 CPU **占用时间**，并不等于真实流逝时间，适用于性能分析。
4. `difftime()` 适用于计算时间间隔，而 `mktime()` 适用于调整时间。

---

## **6. 总结**
- `time_t` 存储时间戳，`struct tm` 存储拆分的日期时间。
- `time()` 获取当前时间戳，`localtime()` / `gmtime()` 拆分时间，`mktime()` 反向转换。
- `strftime()` 格式化时间，`difftime()` 计算时间间隔。
- `clock()` 计算 CPU 执行时间，可用于性能分析。

---
