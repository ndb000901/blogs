# Mysql函数-MiscellaneousFunctions

MySQL 8 中的 **Miscellaneous Functions（杂项函数）** 是一组不易归类到其他功能类别的内置函数，覆盖了从元数据获取、UUID 生成、错误处理、格式化等多种功能。

---

## 1. 常见杂项函数列表

| 函数 | 描述 |
|------|------|
| `UUID()` | 生成一个通用唯一标识符（UUID v1） |
| `UUID_SHORT()` | 返回一个 64 位短 UUID |
| `VERSION()` | 返回 MySQL 当前版本 |
| `CONNECTION_ID()` | 当前线程的连接 ID |
| `DATABASE()` | 当前默认数据库名 |
| `SCHEMA()` | `DATABASE()` 的别名 |
| `USER()` / `CURRENT_USER()` | 当前认证用户 |
| `BENCHMARK(count, expr)` | 对表达式进行基准测试 |
| `FORMAT(number, decimals)` | 数字格式化，带千位分隔 |
| `ROW_COUNT()` | 返回上一语句影响的行数 |
| `FOUND_ROWS()` | 获取上一个 `SQL_CALC_FOUND_ROWS` 的完整结果行数 |
| `GET_DIAGNOSTICS` | SQL 标准中的诊断信息（MySQL 支持有限） |

---

## 2. 示例

---

### 2.1 `UUID()`

生成一个 **标准的 128 位 UUID（版本1）**，格式如下：

```sql
SELECT UUID(); 
-- 示例：'6ccd780c-baba-1026-9564-5b8c656024db'
```

通常用于主键生成或分布式系统中的唯一标识。

---

### 2.2 `UUID_SHORT()`

生成一个 **自增的 64 位整数 UUID**，更短更快，但有风险（跨节点冲突）：

```sql
SELECT UUID_SHORT(); 
-- 示例：139569182720512000
```

> 不适合在多个主库环境使用，因为它依赖当前服务器 ID + 时间戳。

---

### 2.3 `VERSION()`

返回当前 MySQL 服务端版本号。

```sql
SELECT VERSION(); 
-- 示例：'8.0.36'
```

---

### 2.4 `CONNECTION_ID()`

返回当前连接的 thread ID。

```sql
SELECT CONNECTION_ID(); 
-- 示例：123456
```

> 可用于 `KILL CONNECTION_ID()` 手动终止连接。

---

### 2.5 `DATABASE()` / `SCHEMA()`

返回当前默认数据库名称：

```sql
SELECT DATABASE(); 
-- 示例：'testdb'
```

---

### 2.6 `USER()` 与 `CURRENT_USER()`

- `USER()` 返回**登录时指定的用户**
- `CURRENT_USER()` 返回**当前会话认证用户（实际使用的权限用户）**

```sql
SELECT USER(), CURRENT_USER(); 
-- 示例：'dev@localhost', 'readonly@%' 
```

---

### 2.7 `BENCHMARK(count, expr)`

用于性能测试，执行表达式 `count` 次，不返回结果，仅返回 0：

```sql
SELECT BENCHMARK(1000000, SHA2('abc', 512));
```

> 适合在本地测试函数效率，**不能用于生产性能测试**。

---

### 2.8 `FORMAT(number, decimal_places)`

将数字格式化为带千位分隔的字符串。

```sql
SELECT FORMAT(1234567.8912, 2); 
-- 输出：'1,234,567.89'
```

---

### 2.9 `ROW_COUNT()`

返回上一条 SQL（如 `UPDATE`、`DELETE`）影响的行数。

```sql
UPDATE users SET status = 'active' WHERE last_login IS NOT NULL;
SELECT ROW_COUNT(); 
-- 返回：影响的行数
```

---

### 2.10 `FOUND_ROWS()`

必须搭配 `SQL_CALC_FOUND_ROWS` 使用，用于分页查询时获取总行数。

```sql
SELECT SQL_CALC_FOUND_ROWS * FROM users LIMIT 10;
SELECT FOUND_ROWS(); 
-- 返回总行数（不受 LIMIT 限制）
```

> **性能较差**，推荐使用 `COUNT(*) OVER()` 或两次查询替代。

---


