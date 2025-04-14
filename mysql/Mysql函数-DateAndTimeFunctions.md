# Mysql函数-DateAndTimeFunctions

在 MySQL 8 中，**Date and Time Functions（日期与时间函数）** 是日常开发中使用最频繁的一类函数，涵盖了 **获取当前时间、时间计算、格式化、时间差计算、日期解析等操作**。

---

## 1. 函数分类

| 类型             | 代表函数                                            |
|------------------|-----------------------------------------------------|
| 当前时间函数     | `NOW()`, `CURDATE()`, `CURTIME()`, `SYSDATE()`      |
| 时间解析函数     | `STR_TO_DATE()`, `DATE_FORMAT()`, `TIME_FORMAT()`   |
| 时间差函数       | `DATEDIFF()`, `TIMEDIFF()`, `TIMESTAMPDIFF()`       |
| 时间加减函数     | `DATE_ADD()`, `DATE_SUB()`, `ADDDATE()`, `SUBDATE()`|
| 提取单位         | `YEAR()`, `MONTH()`, `DAY()`, `HOUR()`, `MINUTE()`  |
| 其他             | `UNIX_TIMESTAMP()`, `FROM_UNIXTIME()`, `LAST_DAY()` |

---

## 2. 当前时间相关函数

| 函数             | 描述                              | 示例                        |
|------------------|-----------------------------------|-----------------------------|
| `NOW()`          | 当前日期 + 时间（`DATETIME`）     | `2025-04-14 12:30:45`       |
| `CURDATE()`      | 当前日期（`DATE`）                | `2025-04-14`                |
| `CURTIME()`      | 当前时间（`TIME`）                | `12:30:45`                  |
| `SYSDATE()`      | 实时系统时间（区别于 NOW 的延迟）|                             |
| `UTC_TIMESTAMP()`| 当前 UTC 时间                     | `2025-04-14 04:30:45`       |

```sql
SELECT NOW(), CURDATE(), CURTIME(), UTC_TIMESTAMP();
```

---

## 3. 时间加减函数

### `DATE_ADD()` / `DATE_SUB()`

```sql
SELECT DATE_ADD('2025-04-14', INTERVAL 7 DAY);   -- 加7天
SELECT DATE_SUB('2025-04-14', INTERVAL 2 MONTH); -- 减2月
```

---

## 4. 时间差函数

### 4.1 `DATEDIFF(date1, date2)`
- 返回 date1 - date2 的**天数差**

```sql
SELECT DATEDIFF('2025-04-14', '2025-04-01'); -- 13
```

---

### 4.2 `TIMEDIFF(time1, time2)`
- 返回两个 `DATETIME` 或 `TIME` 的时间差（`HH:MM:SS`）

```sql
SELECT TIMEDIFF('12:00:00', '10:30:00'); -- '01:30:00'
```

---

### 4.3 `TIMESTAMPDIFF(unit, datetime1, datetime2)`
- 返回两个时间的差值，单位可以是 YEAR、MONTH、DAY、HOUR、MINUTE、SECOND

```sql
SELECT TIMESTAMPDIFF(DAY, '2025-04-01', '2025-04-14'); -- 13
SELECT TIMESTAMPDIFF(MINUTE, '12:00:00', '12:30:00');  -- 30
```

---

## 5. 时间提取函数

| 函数         | 描述           |
|--------------|----------------|
| `YEAR(date)` | 提取年份       |
| `MONTH(date)`| 提取月份       |
| `DAY(date)`  | 提取天         |
| `HOUR(time)` | 提取小时       |
| `MINUTE(time)`| 提取分钟      |
| `SECOND(time)`| 提取秒         |
| `DAYNAME()`  | 星期几英文名   |
| `MONTHNAME()`| 月份英文名     |

```sql
SELECT YEAR(NOW()), MONTH(NOW()), DAY(NOW());
```

---

## 6. 格式化与解析

### 6.1 `DATE_FORMAT(date, format)`
- 将日期转为指定格式的字符串

```sql
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s'); -- '2025-04-14 12:30:45'
```

### 6.2 `STR_TO_DATE(str, format)`
- 将字符串按格式解析为日期

```sql
SELECT STR_TO_DATE('2025-04-14', '%Y-%m-%d');
```

---

## 7. UNIX 时间戳

| 函数                 | 描述                     |
|----------------------|--------------------------|
| `UNIX_TIMESTAMP()`   | 当前时间戳（秒）         |
| `FROM_UNIXTIME(ts)`  | 时间戳转日期             |

```sql
SELECT UNIX_TIMESTAMP();         -- 例如 1744627845
SELECT FROM_UNIXTIME(1744627845); -- '2025-04-14 12:30:45'
```

---

## 8. 其他常用函数

| 函数            | 描述                          |
|-----------------|-------------------------------|
| `LAST_DAY(date)`| 给定日期的**当月最后一天**     |
| `WEEK(date)`    | 返回所在年的第几周             |
| `QUARTER(date)` | 返回第几季度（1-4）            |
| `MAKEDATE(y,d)` | 构造日期：第 y 年第 d 天       |

```sql
SELECT LAST_DAY('2025-02-10');      -- '2025-02-28'
SELECT QUARTER('2025-04-14');       -- 2
SELECT MAKEDATE(2025, 100);         -- '2025-04-10'
```

---



