# Mysql函数-NumericFunctions

在 MySQL 8 中，**Numeric Functions（数值函数）** 提供了丰富的数学、算术和数字处理能力，适用于各种数据处理场景，如四舍五入、取整、取绝对值、对数、幂运算等。

---

## 1. 常见 Numeric 函数列表

| 函数 | 描述 |
|------|------|
| `ABS(X)` | 返回绝对值 |
| `CEIL(X)` / `CEILING(X)` | 向上取整 |
| `FLOOR(X)` | 向下取整 |
| `ROUND(X[, D])` | 四舍五入（可指定小数位） |
| `TRUNCATE(X, D)` | 截断数字到指定小数位 |
| `MOD(N, M)` / `N % M` | 取模运算 |
| `POWER(X, Y)` / `POW(X, Y)` | 幂运算 |
| `SQRT(X)` | 平方根 |
| `EXP(X)` | e 的 X 次幂 |
| `LOG(X)` / `LOG(B, X)` | 自然对数 / 指定底对数 |
| `LOG2(X)` / `LOG10(X)` | 以 2 / 10 为底的对数 |
| `SIGN(X)` | 返回 -1/0/1 |
| `RAND([N])` | 返回 0~1 之间的随机数 |
| `PI()` | 返回圆周率 π |
| `RADIANS(X)` / `DEGREES(X)` | 角度与弧度转换 |
| `LEAST(X, Y, ...)` / `GREATEST(X, Y, ...)` | 最小 / 最大值 |
| `CONV(N, from_base, to_base)` | 进制转换 |
| `BIN(N)` / `OCT(N)` / `HEX(N)` | 转二 / 八 / 十六进制字符串 |

---

## 2. 示例

---

### 2.1 `ABS(X)`
返回绝对值：

```sql
SELECT ABS(-123);  -- 123
```

---

### 2.2 `CEIL(X)` / `CEILING(X)`
向上取整（最小的整数 ≥ X）：

```sql
SELECT CEIL(1.2);  -- 2
```

---

### 2.3 `FLOOR(X)`
向下取整（最大整数 ≤ X）：

```sql
SELECT FLOOR(1.9);  -- 1
```

---

### 2.4 `ROUND(X[, D])`
四舍五入到小数点 D 位（默认 D=0）：

```sql
SELECT ROUND(1.456, 2);  -- 1.46
```

---

### 2.5 `TRUNCATE(X, D)`
截断而非四舍五入：

```sql
SELECT TRUNCATE(1.456, 2);  -- 1.45
```

---

### 2.6 `MOD(N, M)` / `N % M`
模运算：

```sql
SELECT MOD(10, 3);  -- 1
SELECT 10 % 3;      -- 1
```

---

### 2.7 `POWER(X, Y)` / `POW(X, Y)`
幂运算：

```sql
SELECT POWER(2, 3);  -- 8
```

---

### 2.8 `SQRT(X)`
平方根：

```sql
SELECT SQRT(9);  -- 3
```

---

### 2.9 `EXP(X)`
e 的 x 次幂：

```sql
SELECT EXP(1);  -- 约等于 2.718
```

---

### 2.10 `LOG(X)` / `LOG(B, X)`
对数：

```sql
SELECT LOG(10);        -- 自然对数 ln(10)
SELECT LOG(2, 8);       -- 以 2 为底的对数，返回 3
```

---

### 2.11 `LOG2(X)` / `LOG10(X)`
以 2 或 10 为底：

```sql
SELECT LOG2(8);   -- 3
SELECT LOG10(1000); -- 3
```

---

### 2.12 `SIGN(X)`
符号函数：

```sql
SELECT SIGN(-10);  -- -1
SELECT SIGN(0);    -- 0
SELECT SIGN(10);   -- 1
```

---

### 2.13 `RAND([N])`
生成 0～1 的随机数（种子可指定）：

```sql
SELECT RAND();       -- 例如 0.23718
SELECT RAND(100);    -- 固定种子，结果可重复
```

---

### 2.14 `PI()`
圆周率 π：

```sql
SELECT PI();  -- 3.1415926535
```

---

### 2.15 `RADIANS(X)` / `DEGREES(X)`
角度 ↔ 弧度转换：

```sql
SELECT RADIANS(180);  -- π ≈ 3.14159
SELECT DEGREES(PI()); -- 180
```

---

### 2.16 `LEAST(X, Y, ...)` / `GREATEST(X, Y, ...)`
找最小或最大值：

```sql
SELECT LEAST(3,5,7,1);     -- 1
SELECT GREATEST(3,5,7,1);  -- 7
```

---

### 2.17 `CONV(N, from_base, to_base)`
进制转换（返回字符串）：

```sql
SELECT CONV('A', 16, 10);   -- '10'
SELECT CONV(10, 10, 2);     -- '1010'
```

---

### 2.18 `BIN(N)` / `OCT(N)` / `HEX(N)`
十进制转 2、8、16 进制：

```sql
SELECT BIN(10);   -- '1010'
SELECT OCT(10);   -- '12'
SELECT HEX(255);  -- 'FF'
```

---



