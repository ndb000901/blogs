# C标准库10-locale

`locale.h` 是 C 语言标准库中的一个头文件，**提供了一系列函数和常量用于管理程序的区域设置信息（locale）**，例如**语言、数字格式、货币符号、日期格式等**。它的主要作用是**支持国际化和本地化**，使程序能够适应不同地区的用户习惯。

---

## **1. `locale.h` 的作用**
- **支持多语言环境**（例如 `en_US.UTF-8`、`zh_CN.UTF-8`）。
- **控制数字、日期、货币格式**（小数点 `.` vs `,`，千位分隔符）。
- **影响 C 库的一些函数行为**（如 `printf()` 如何显示千分位）。
- **提供 `setlocale()` 以更改程序的区域设置**。

---

## **2. `locale.h` 定义的结构和常量**
`locale.h` 主要定义了：
- **`struct lconv` 结构体**（存储本地化信息）
- **一组宏定义**（用于 `setlocale()` 设置区域）
- **函数声明**（如 `setlocale()` 和 `localeconv()`）

### **2.1 `struct lconv` 结构体**
`lconv` 结构体存储**当前区域**（locale）**的数值、货币、千分位等格式**：
```c
struct lconv {
    /* 数字格式 */
    char *decimal_point;      // 小数点符号
    char *thousands_sep;      // 千位分隔符
    char *grouping;           // 千位分隔规则

    /* 货币格式 */
    char *currency_symbol;    // 货币符号
    char *int_curr_symbol;    // 国际货币符号（如 "USD "）
    char *mon_decimal_point;  // 货币小数点符号
    char *mon_thousands_sep;  // 货币千分位符号
    char *mon_grouping;       // 货币千位分隔规则
    char *positive_sign;      // 正数符号
    char *negative_sign;      // 负数符号
    char int_frac_digits;     // 国际货币小数位数
    char frac_digits;         // 货币小数位数
    char p_cs_precedes;       // 货币符号是否在数值前
    char p_sep_by_space;      // 货币符号和数值之间是否有空格
    char n_cs_precedes;       // 负货币符号是否在数值前
    char n_sep_by_space;      // 负货币符号和数值之间是否有空格
    char p_sign_posn;         // 正数符号位置
    char n_sign_posn;         // 负数符号位置
};
```
### **2.2 预定义的区域宏**
`setlocale()` 可用于设置程序的区域，`locale.h` 提供了一些预定义的宏：
| 宏 | 说明 |
|----|------|
| `LC_ALL` | 影响所有的本地化类别 |
| `LC_COLLATE` | 影响字符串比较（`strcoll()`） |
| `LC_CTYPE` | 影响字符分类（`isalpha()` 等） |
| `LC_MONETARY` | 影响货币格式 |
| `LC_NUMERIC` | 影响数字格式（小数点符号） |
| `LC_TIME` | 影响日期格式 |
| `LC_MESSAGES` | 影响系统消息（POSIX 扩展） |

---

## **3. `locale.h` 的常用函数**
### **3.1 `setlocale()`**
用于**获取或设置程序的当前区域设置**：
```c
char *setlocale(int category, const char *locale);
```
- `category` 选择本地化类别（如 `LC_ALL`）。
- `locale`：
  - `""` 采用用户的环境变量（如 `LANG`）。
  - `"C"` 采用默认的 C 语言环境（无本地化）。
  - `NULL` 只返回当前的区域。

**示例：**
```c
#include <stdio.h>
#include <locale.h>

int main() {
    printf("当前区域: %s\n", setlocale(LC_ALL, NULL));
    setlocale(LC_ALL, "zh_CN.UTF-8");
    printf("修改后区域: %s\n", setlocale(LC_ALL, NULL));
    return 0;
}
```
**输出（在中文系统上）：**
```
当前区域: C
修改后区域: zh_CN.UTF-8
```

---

### **3.2 `localeconv()`**
用于**获取当前区域的数值和货币格式**：
```c
struct lconv *localeconv(void);
```
**示例：**
```c
#include <stdio.h>
#include <locale.h>

int main() {
    setlocale(LC_ALL, "en_US.UTF-8");
    struct lconv *loc = localeconv();

    printf("小数点符号: %s\n", loc->decimal_point);
    printf("千分位分隔符: %s\n", loc->thousands_sep);
    printf("货币符号: %s\n", loc->currency_symbol);
    
    return 0;
}
```
**可能的输出（在美式区域设置下）：**
```
小数点符号: .
千分位分隔符: ,
货币符号: $
```

---

## **4. `locale.h` 在不同环境下的作用**
### **4.1 在 UNIX/Linux 下**
在 UNIX/Linux 系统中，区域环境变量通常存储在 `LANG`、`LC_ALL` 等环境变量中，例如：
```sh
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```
可以用 `locale` 命令查看当前系统的区域：
```sh
locale
```

### **4.2 在 Windows 下**
Windows 使用代码页（Code Page）来管理区域，例如：
```c
setlocale(LC_ALL, "chs");  // 设置为简体中文
setlocale(LC_ALL, "enu");  // 设置为英语
```
但在 Windows 上区域名称格式可能不同，建议查阅 `setlocale()` 的文档。

---

## **5. `locale.h` 的应用场景**
### **5.1 让字符串排序遵循区域规则**
`strcoll()` 函数按照区域规则排序：
```c
#include <stdio.h>
#include <locale.h>
#include <string.h>

int main() {
    setlocale(LC_COLLATE, "fr_FR.UTF-8");  // 设置为法语
    char str1[] = "éclair";
    char str2[] = "eclair";

    if (strcoll(str1, str2) < 0)
        printf("éclair 排在 eclair 之前\n");
    else
        printf("eclair 排在 éclair 之前\n");

    return 0;
}
```
**法语环境下 `éclair` 会排在 `eclair` 之后，而 C 语言默认 `strcmp()` 则不会**。

---

### **5.2 让小数点和千位分隔符遵循区域规则**
在欧洲，大部分国家**使用 `,` 作为小数点**，而在美国则使用 `.`：
```c
#include <stdio.h>
#include <locale.h>

int main() {
    setlocale(LC_NUMERIC, "fr_FR.UTF-8");  // 法国区域

    struct lconv *loc = localeconv();
    printf("法国的小数点符号: %s\n", loc->decimal_point);

    return 0;
}
```
**输出（法国地区）：**
```
法国的小数点符号: ,
```

---

## **6. `locale.h` 的注意事项**
1. **`setlocale()` 影响整个进程**，它**不是线程安全**的。
2. **不同系统的区域名称不同**，在 Windows 需要用 `"chs"`，Linux 可能是 `"zh_CN.UTF-8"`。
3. **某些 C 标准库不支持 `LC_MESSAGES`**，需检查具体实现。
4. **默认 `C` 语言环境不会使用本地化规则**，如果需要，必须手动调用 `setlocale()`。

---

## **7. 总结**
- `locale.h` 提供**国际化支持**，可调整**数字、货币、日期格式**等。
- 主要函数：
  - `setlocale()` 设置区域环境
  - `localeconv()` 获取本地化数值/货币格式
  - `strcoll()` 进行本地化字符串比较
- 在多语言程序、财务应用、文本排序等场景非常重要。
