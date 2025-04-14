# Mysql函数-BitFunctions

Bit Functions（位函数）在 MySQL 中是处理二进制和位运算的利器，常用于权限控制、状态标志字段、压缩存储等场景，尤其在低层系统或高性能数据压缩中很常见。


---

## 1. 常用 Bit Functions 

### 1.1 `BIT_AND(expr)`
- **功能**：对组内所有值执行按位与（`&`）。
- **常用于**：判断某个位在所有数据中是否都为 1。
- **示例**：
  ```sql
  SELECT BIT_AND(flags) FROM tasks;
  ```

- **注意事项**：
    - 只要某行某一位为 0，结果该位就是 0。
    - `NULL` 会导致结果为 NULL。

---

### 1.2 `BIT_OR(expr)`
- **功能**：对组内所有值执行按位或（`|`）。
- **用途**：常用于合并标志位。
- **示例**：
  ```sql
  SELECT BIT_OR(permission_flags) FROM users;
  ```

- **技巧**：快速求出所有记录中出现过哪些标志位。

---

### 1.3 `BIT_XOR(expr)`
- **功能**：对组内所有值执行按位异或（`^`）。
- **用途**：奇偶配对、判断是否为偶数次出现。
- **示例**：
  ```sql
  SELECT BIT_XOR(code) FROM log_events;
  ```

- **用途案例**：用于查找落单的某条记录（奇数次出现）。

---

### 1.4 `&`、`|`、`^`、`~`、`<<`、`>>` —— 位运算符
- MySQL 原生支持这些位运算操作。

#### 示例：
```sql
SELECT 5 & 3;  -- 1 (101 & 011)
SELECT 5 | 3;  -- 7 (101 | 011)
SELECT 5 ^ 3;  -- 6 (101 ^ 011)
SELECT ~5;     -- -6（补码）
SELECT 1 << 3; -- 8
SELECT 8 >> 2; -- 2
```

---

## 2. 使用场景

### 权限字段 bitmask
```sql
-- 假设每个权限对应一位：
-- 1 = 读，2 = 写，4 = 执行

-- 判断是否包含写权限：
SELECT * FROM users WHERE (permissions & 2) = 2;
```

### 标志位压缩（状态组合字段）
```sql
-- 某个字段 status_flags 表示不同状态：
-- bit0 = 已读，bit1 = 已归档，bit2 = 已同步
-- 查询所有“已归档”的：
SELECT * FROM messages WHERE (status_flags & 2) = 2;
```

---

## 3. 存储层面说明

- **`BIT(n)` 类型字段**：MySQL 支持 BIT 类型用于存储定长位字段。
  ```sql
  CREATE TABLE t (
    b BIT(8)
  );
  ```

- 可以通过 `b | 4` 等方式做位更新。

- **`SET` 类型**也能实现类似功能，但不如 BIT 运算灵活。

---

## 4. 注意事项

1. `BIT_AND` / `BIT_OR` 等聚合函数在组内存在 NULL，会返回 NULL，建议用 `IFNULL` 包裹：
   ```sql
   SELECT BIT_AND(IFNULL(flags, 0)) FROM tasks;
   ```

2. `BIT` 字段取出时会变成二进制，需要用 `BIN()` 或 `CONV()` 转换：
   ```sql
   SELECT BIN(flag_bits), CONV(flag_bits, 2, 10) FROM t;
   ```

3. 位移运算符不能用于负数或非整数值。

---

## 5. 配合 JSON、窗口函数

### 示例：JSON + Bit 位权限解码
```sql
SELECT
  user_id,
  JSON_OBJECT(
    'can_read', (permissions & 1) = 1,
    'can_write', (permissions & 2) = 2,
    'can_execute', (permissions & 4) = 4
  ) AS permission_json
FROM users;
```

### 窗口函数 + 位运算
```sql
SELECT
  project_id,
  BIT_OR(flags) OVER (PARTITION BY project_id) AS combined_flags
FROM tasks;
```

---


