# Mysql函数-PerformanceSchemaFunctions

MySQL 8 中的 **`Performance Schema Functions`**（性能模式函数）用于访问和管理 Performance Schema（性能模式）收集到的数据。Performance Schema 是一个用于监控 MySQL 内部执行过程、分析性能瓶颈的重要工具。

---

## 1. Performance Schema 简介

- Performance Schema（简称 P_S）本质上是一个只读的系统数据库。
- 它通过采样或事件捕获的方式，记录线程、I/O、锁、查询、等待事件等执行细节。
- `performance_schema` 函数和表为 DBA 和开发者提供了监控与诊断工具。

---

## 2. Performance Schema Functions 列表

| 函数名 | 简介 |
|--------|------|
| `PS_CURRENT_THREAD_ID()` | 返回当前线程的 P_S 线程 ID |
| `PS_THREAD_ID(processlist_id)` | 根据 `SHOW PROCESSLIST` 的 ID 返回对应的 P_S 线程 ID |

---

## 3. 函数详解

### 3.1 `PS_CURRENT_THREAD_ID()`

- **作用**：返回当前会话所对应的 Performance Schema 线程 ID（不是 `SHOW PROCESSLIST` 中看到的 ID）。
- **用途**：用于在 Performance Schema 的 `threads`、`events_waits_summary_by_thread_by_event_name` 等表中关联线程相关信息。

**示例**：

```sql
SELECT PS_CURRENT_THREAD_ID();
-- 结果示例：23
```

然后可以查询该线程对应的事件：

```sql
SELECT * FROM performance_schema.threads WHERE THREAD_ID = PS_CURRENT_THREAD_ID();
```

---

### 3.2 `PS_THREAD_ID(processlist_id)`

- **作用**：将 `SHOW PROCESSLIST` 中的 `ID`（连接 ID）转换成 Performance Schema 中的线程 ID。
- **用途**：当你从 `SHOW PROCESSLIST` 中拿到某个连接的 ID，想进一步去 `performance_schema` 查询该连接的执行详情时需要用它转换 ID。

**示例**：

```sql
-- 获取某个连接的 Performance Schema 线程 ID
SELECT PS_THREAD_ID(123);  -- 123 是 processlist 中的 ID
-- 可能返回 45（P_S 线程 ID）
```

再用它去查具体事件、等待：

```sql
SELECT * FROM performance_schema.events_statements_history WHERE THREAD_ID = PS_THREAD_ID(123);
```

---

## 4. 常见应用场景

| 目的 | 使用方法 |
|------|----------|
| 监控当前会话行为 | 结合 `PS_CURRENT_THREAD_ID()` 与 `events_statements_current` 表 |
| 调查某连接的慢语句 | `PS_THREAD_ID()` 转换连接 ID，再查 `events_statements_history_long` |
| 分析某连接的锁等待 | 查 `performance_schema.events_waits_history` |

---

## 5. 注意事项

- 不是所有线程都一定出现在 `performance_schema.threads` 表中，尤其是系统线程（如 `system user`）。
- Performance Schema 必须启用相关监控开关才能记录详细信息（如 `statements`, `waits`, `stages` 等）。
- `THREAD_ID` 是一个大整数，和 `SHOW PROCESSLIST` 中的连接 ID 并非同一个含义。

---

## 6. 使用示例

### 查询当前会话执行的 SQL

```sql
SELECT EVENT_ID, SQL_TEXT
FROM performance_schema.events_statements_current
WHERE THREAD_ID = PS_CURRENT_THREAD_ID();
```

### 查看某连接当前执行的语句

```sql
-- 替换成实际连接 ID
SELECT EVENT_ID, SQL_TEXT
FROM performance_schema.events_statements_current
WHERE THREAD_ID = PS_THREAD_ID(233);
```

---

