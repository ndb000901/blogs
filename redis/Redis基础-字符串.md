# Redis基础-字符串


## 1. String 类型概述

在 Redis 中，String 是最基本的数据类型。它不仅支持字符串，还支持数字（二进制安全），可以存储：

- 文本字符串（如 "hello"）
- 整数、浮点数（可用于自增等操作）
- 二进制数据（如图片、音频的 Base64 编码、JSON 字符串等）

单个 String 类型的最大容量是 **512MB**。

---

## 2. 常用命令

### 基础命令

| 命令 | 作用 |
|------|------|
| `SET key value` | 设置字符串 |
| `GET key` | 获取字符串 |
| `DEL key` | 删除 key |
| `SETNX key value` | 如果 key 不存在则设置 |
| `SETEX key seconds value` | 设置带过期时间的值 |
| `PSETEX key milliseconds value` | 设置带毫秒级过期时间的值 |
| `MSET k1 v1 k2 v2` | 批量设置 |
| `MGET k1 k2` | 批量获取 |
| `GETSET key new_value` | 获取旧值并设置新值 |
| `STRLEN key` | 获取字符串长度 |

### 数值操作（仅对整数/浮点字符串有效）

| 命令 | 说明 |
|------|------|
| `INCR key` | 自增 1 |
| `INCRBY key increment` | 自增指定值 |
| `DECR key` | 自减 1 |
| `DECRBY key decrement` | 自减指定值 |
| `INCRBYFLOAT key float_val` | 浮点自增 |

### 位操作

| 命令 | 说明 |
|------|------|
| `SETBIT key offset value` | 设置指定位为 0 或 1 |
| `GETBIT key offset` | 获取指定位 |
| `BITCOUNT key` | 统计字符串中值为 1 的 bit 个数 |
| `BITOP AND\|OR\|XOR\|NOT destkey key1 [key2 ...]` | 位运算 |

### 子串处理

| 命令 | 说明 |
|------|------|
| `GETRANGE key start end` | 获取子字符串 |
| `SETRANGE key offset value` | 替换子字符串 |

---

## 3. 底层编码实现（即 `encoding`）

Redis 会根据字符串内容的不同自动选择合适的底层编码方式（用 `OBJECT ENCODING key` 查看）：

### 3.1 **int（整数编码）**
- 如果值是整数，且在 `long` 范围内，Redis 会直接存储为整数。
- 优点：节省内存，快速运算。
- 示例：
  ```redis
  SET myint 123
  ```

### 3.2 **embstr（短字符串编码）**
- 长度 ≤ 44 字节时（默认），使用 embstr 编码。
- 内存连续分配，分配速度快。
- 存储结构：`redisObject + SDS（simple dynamic string）` 一起分配。
- 特别适合短字符串（如常见的 JSON ID、UUID 等）。

### 3.3 **raw（长字符串编码）**
- 长度 > 44 字节的普通字符串。
- `redisObject` 和 `SDS` 分开分配。
- 占用内存稍多，但可以支持大数据量。

> **注意**：这些编码对用户透明，Redis 会自动选择最优方案。

---

## 4. 性能特性与优化建议

- **短字符串首选 embstr，速度快，减少碎片化**
- **多次更新字符串可能导致内存碎片**，可考虑定期 `REWRITE` 或使用 `MEMORY PURGE`
- 对二进制数据传输，可 Base64 编码后存入，注意长度不要超过 512MB
- Redis 是单线程，**长字符串操作耗时较长**，尽量不要在热点 key 上操作巨大的字符串

---


