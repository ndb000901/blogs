# C标准库11-math

`math.h` 是 C 标准库中的 **数学函数头文件**，提供 **基本数学运算函数**，如三角函数、指数对数函数、幂函数、取整函数等。

---

## **1. `math.h` 头文件的引入**
在 C 代码中使用 `math.h` 需要包含该头文件：
```c
#include <math.h>
```
**编译时需要链接 `libm` 数学库**：
```bash
gcc main.c -o main -lm
```
🔹 `-lm` 选项告诉编译器链接数学库（`libm`）。

---

## **2. `math.h` 提供的常量**
| **常量** | **说明** |
|---------|---------|
| `M_PI`  | 圆周率（π ≈ 3.141592653589793） |
| `M_E`   | 自然对数的底数（e ≈ 2.718281828459045） |
| `M_LOG2E` | `log₂(e)` ≈ 1.442695 |
| `M_LOG10E` | `log₁₀(e)` ≈ 0.434294 |
| `M_LN2`  | `ln(2)` ≈ 0.693147 |
| `M_LN10` | `ln(10)` ≈ 2.302585 |
| `M_SQRT2` | `√2` ≈ 1.414213 |

示例：
```c
#include <stdio.h>
#include <math.h>

int main() {
    printf("PI = %f\n", M_PI);
    printf("e  = %f\n", M_E);
    return 0;
}
```

---

## **3. 常用数学函数**
### **(1) 幂运算**
| **函数** | **作用** | **示例** |
|----------|---------|---------|
| `double pow(double x, double y)` | `x^y` | `pow(2, 3) = 8` |
| `double sqrt(double x)` | `√x` | `sqrt(9) = 3` |
| `double cbrt(double x)` | 立方根 | `cbrt(8) = 2` |
| `double hypot(double x, double y)` | `√(x² + y²)`（勾股定理） | `hypot(3, 4) = 5` |

示例：
```c
printf("2^3 = %f\n", pow(2, 3));
printf("sqrt(9) = %f\n", sqrt(9));
printf("cbrt(8) = %f\n", cbrt(8));
printf("hypot(3, 4) = %f\n", hypot(3, 4));
```

---

### **(2) 对数 & 指数**
| **函数** | **作用** | **示例** |
|----------|---------|---------|
| `double log(double x)` | `ln(x)` 自然对数 | `log(2.718) ≈ 1` |
| `double log10(double x)` | `log₁₀(x)` | `log10(100) = 2` |
| `double log2(double x)` | `log₂(x)` | `log2(8) = 3` |
| `double exp(double x)` | `e^x` | `exp(1) ≈ 2.718` |
| `double exp2(double x)` | `2^x` | `exp2(3) = 8` |

示例：
```c
printf("ln(2.718) = %f\n", log(2.718));
printf("log10(100) = %f\n", log10(100));
printf("log2(8) = %f\n", log2(8));
printf("e^1 = %f\n", exp(1));
printf("2^3 = %f\n", exp2(3));
```

---

### **(3) 三角函数**
| **函数** | **作用** | **示例** |
|----------|---------|---------|
| `double sin(double x)` | 正弦（弧度制） | `sin(M_PI/2) = 1` |
| `double cos(double x)` | 余弦（弧度制） | `cos(0) = 1` |
| `double tan(double x)` | 正切（弧度制） | `tan(M_PI/4) = 1` |
| `double asin(double x)` | 反正弦 | `asin(1) = π/2` |
| `double acos(double x)` | 反余弦 | `acos(1) = 0` |
| `double atan(double x)` | 反正切 | `atan(1) = π/4` |
| `double atan2(double y, double x)` | 计算 `y/x` 的角度 | `atan2(1, 1) = π/4` |

示例：
```c
printf("sin(PI/2) = %f\n", sin(M_PI/2));
printf("cos(0) = %f\n", cos(0));
printf("tan(PI/4) = %f\n", tan(M_PI/4));
printf("asin(1) = %f\n", asin(1));
printf("atan2(1, 1) = %f\n", atan2(1, 1));
```

---

### **(4) 取整 & 舍入**
| **函数** | **作用** | **示例** |
|----------|---------|---------|
| `double ceil(double x)` | 向上取整 | `ceil(2.3) = 3` |
| `double floor(double x)` | 向下取整 | `floor(2.9) = 2` |
| `double round(double x)` | 四舍五入 | `round(2.5) = 3` |
| `double trunc(double x)` | 截断小数部分 | `trunc(2.9) = 2` |

示例：
```c
printf("ceil(2.3) = %f\n", ceil(2.3));
printf("floor(2.9) = %f\n", floor(2.9));
printf("round(2.5) = %f\n", round(2.5));
printf("trunc(2.9) = %f\n", trunc(2.9));
```

---

### **(5) 绝对值 & 符号**
| **函数** | **作用** | **示例** |
|----------|---------|---------|
| `double fabs(double x)` | 绝对值 | `fabs(-5) = 5` |
| `double copysign(double x, double y)` | 复制符号 | `copysign(2, -3) = -2` |

示例：
```c
printf("fabs(-5) = %f\n", fabs(-5));
printf("copysign(2, -3) = %f\n", copysign(2, -3));
```

---

## **4. `math.h` 的特点**
- **所有函数返回 `double` 类型**，如果需要 `float` 或 `long double` 版本，使用：
  - `sinf()`, `cosf()`, `tanf()` （`float` 版）
  - `sinl()`, `cosl()`, `tanl()` （`long double` 版）

- **大多数函数的输入参数必须是** `double`，否则需要显式转换：
  ```c
  int x = 10;
  double y = sqrt((double)x);
  ```

---

## **5. 总结**
🔹 **`math.h` 提供数学运算函数**
- **指数 & 对数**（`exp()`、`log()`、`pow()`）
- **三角函数**（`sin()`、`cos()`、`tan()`）
- **取整 & 舍入**（`ceil()`、`floor()`、`round()`）
- **绝对值 & 符号**（`fabs()`、`copysign()`）

🔹 **编译时需要 `-lm` 选项**
```bash
gcc main.c -o main -lm
```

🔹 **某些常量如 `M_PI` 可能需要 `#define _USE_MATH_DEFINES`**（在某些编译器中，如 MSVC）。
