# Mysql基础-DELETE

## 1. 基本语法

```sql
DELETE FROM 表名
[WHERE 条件]
[LIMIT N];
```

### 示例：
```sql
DELETE FROM users
WHERE id = 100;
```

> **不加 `WHERE` 会删除整张表中的所有数据，慎用！**

---

## 2. 常见用法示例

### 删除单条记录：
```sql
DELETE FROM users WHERE id = 1;
```

### 删除多条记录（满足条件）：
```sql
DELETE FROM orders WHERE status = 'CANCELLED';
```

### 配合 `LIMIT` 批量删除（防止锁表）：
```sql
DELETE FROM logs
WHERE created_at < '2024-01-01'
LIMIT 1000;
```

配合脚本循环执行可实现分批删除：

```sql
-- 每次运行下面语句直到返回 0 行
DELETE FROM logs
WHERE created_at < '2024-01-01'
LIMIT 1000;
```

---

## 3. DELETE + JOIN（多表删除）

### 删除一张表中满足 JOIN 条件的记录：

```sql
DELETE u
FROM users u
JOIN user_scores s ON u.id = s.user_id
WHERE s.year = 2023;
```

### 同时删除多张表中的记录（需要开启 `foreign_key_checks = 0`）：
```sql
DELETE u, s
FROM users u
JOIN user_scores s ON u.id = s.user_id
WHERE s.year = 2023;
```

> 注意：只有在 **InnoDB 且表具有关联外键** 时能这样删除。

---

## 4. DELETE 与 TRUNCATE 对比

| 操作        | DELETE                 | TRUNCATE                 |
|-------------|------------------------|--------------------------|
| 支持 WHERE  | 支持                  | 不支持                  |
| 生成日志    | 每行生成 binlog、undo log | 更类似于 DDL 操作          |
| 是否可回滚  | 可在事务中回滚            | 不可回滚                 |
| 重置自增 ID | 默认不重置              | 会将 AUTO_INCREMENT 重置 |
| 执行速度    | 较慢（逐行删除）           | 快（直接重建表）             |

---

## 5. DELETE 的事务支持

```sql
START TRANSACTION;

DELETE FROM accounts WHERE balance < 0;

COMMIT;
```

> 如果发现误删可以 `ROLLBACK`，除非用了 `autocommit = 1` 或 TRUNCATE。

---

## 6. 防止误删除

**开启安全模式**：
   ```sql
   SET SQL_SAFE_UPDATES = 1;
   ```

**加 LIMIT 限制删除行数**：
   ```sql
   DELETE FROM logs WHERE created_at < '2023-01-01' LIMIT 100;
   ```

**先 SELECT 再 DELETE**（确认影响记录）：
   ```sql
   SELECT * FROM users WHERE status = 'inactive';
   DELETE FROM users WHERE status = 'inactive';
   ```

---

## 7. DELETE 性能优化建议

| 技巧                     | 原因与说明                                               |
|--------------------------|----------------------------------------------------------|
| `WHERE` 条件字段加索引  | 避免全表扫描，提高定位效率                              |
| 分批删除 + LIMIT         | 避免锁表时间太长                                         |
| 避免函数在 `WHERE` 中   | 如 `WHERE YEAR(created_at) = 2022`，会阻止索引生效       |
| 批量删除大表时禁用外键检查 | `SET foreign_key_checks = 0;` 提高删除效率               |
| 分区表删除指定分区      | 如果使用分区表，可使用 `ALTER TABLE t DROP PARTITION` 更快 |

---

## 8. 常见错误

| 错误代码 | 描述                               | 原因                                      |
|----------|------------------------------------|-------------------------------------------|
| 1175     | 安全模式禁止全表删除              | 开启了 `SQL_SAFE_UPDATES` 没有加条件      |
| 1451     | Cannot delete or update a parent row | 外键约束冲突，需先删除子表或关闭外键检查 |
| 1064     | SQL 语法错误                       | 通常字段名、表名拼写错误或缺少关键字     |

---



