# Redis基础-位图

## 1. Bitmap

**位图（Bitmap）** 并不是 Redis 的一种独立数据类型，而是基于 **String 类型的位操作扩展** —— 可以将一个 `String` 看成一串连续的二进制位（bit），每个位是 0 或 1。可以按位设置、获取、统计。

> Redis 的 String 最长支持 512MB，也就是说可以操作多达 **2^32 ≈ 40 亿个位**！

---

## 2. 底层原理

Redis 使用普通的字符串（byte array），但允许你：

- 对某个“位”设置值（SETBIT）
- 获取某个位的值（GETBIT）
- 统计 1 的数量（BITCOUNT）
- 执行位操作（AND / OR / XOR / NOT）

位图是面向 bit 的高密度数据结构，适用于空间效率非常重要的场景。

---

## 3. 常用命令

### 3.1 设置某个位的值

```bash

SETBIT key offset value
```

- offset：第几位（从 0 开始）
- value：0 或 1

```bash

SETBIT user:active 100 1
```

表示将 `user:active` 字符串的第 101 位设置为 1。

---

### 3.2 获取某个位的值

```bash

GETBIT key offset
```

返回 0 或 1。

---

### 3.3 统计总共多少位是 1

```bash

BITCOUNT key [start] [end]
```

- 统计 key 中值为 1 的位的个数。
- 可选的 start 和 end 是字节（Byte）范围，而不是 bit。

```bash

BITCOUNT user:active       # 统计所有 1 的数量
BITCOUNT user:active 0 1   # 统计第 0~1 字节中的 1 数量（即前 16 位）
```

---

### 3.4 查找第一个设置为 1 或 0 的 bit 位置

```bash

BITPOS key bit [start] [end]
```

返回第一个 bit 出现的位置（单位是 bit）。

```bash

BITPOS user:active 1
```

---

### 3.5 多个位图之间做逻辑运算

```bash

BITOP operation destkey key1 [key2 ...]
```

- operation 支持：AND / OR / XOR / NOT
- destkey 是结果写入的 key

```bash

BITOP AND common user:day1 user:day2
```

找出连续两天活跃的用户。

---

## 3.6 Redis 7.0 新增命令（位域操作）

更高级的位操作通过 `BITFIELD` 命令实现，支持多 bit 为一组的整数读写（支持符号/无符号，支持溢出处理）。

```bash

BITFIELD key GET u8 0 SET i5 0 4 INCRBY u4 4 1
```

功能说明：

- `GET u8 0`：从第 0 位读取一个 **8 位无符号数**。
- `SET i5 0 4`：从第 0 位写入一个 **5 位有符号数，值为 4**。
- `INCRBY u4 4 1`：从第 4 位的 **4 位无符号数中加 1**。

这是更强大的进阶位图操作，可以用作小整数数组、行为计数器等。

---


