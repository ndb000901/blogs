# Mysql基础-INSERT

## 1. INSERT 基础语法

```sql
INSERT INTO 表名 [(字段1, 字段2, ...)] VALUES (值1, 值2, ...);
```

### 示例：
```sql
INSERT INTO users (id, name, email) VALUES (1, '张三', 'zhang@example.com');
```

> 字段顺序要和 `VALUES` 中一一对应。

---

## 2. 插入多行数据

```sql
INSERT INTO users (id, name) VALUES 
(2, '李四'),
(3, '王五'),
(4, '赵六');
```

> 推荐批量插入，减少网络与解析开销，**比单条 insert 快很多**。

---

## 3. 默认值插入

```sql
INSERT INTO users (name) VALUES ('匿名');
```
如果某些字段设置了 `DEFAULT` 值，可以不提供。

---

## 4. INSERT 变体与高级用法

### 4.1 `INSERT IGNORE`
忽略错误（如主键冲突）

```sql
INSERT IGNORE INTO users (id, name) VALUES (1, '重复ID'); -- 被忽略
```

### 4.2 `INSERT ... ON DUPLICATE KEY UPDATE`
主键或唯一索引冲突时执行更新

```sql
INSERT INTO users (id, name) 
VALUES (1, '张三新名字') 
ON DUPLICATE KEY UPDATE name = VALUES(name);
```

也可以更新多个字段：
```sql
ON DUPLICATE KEY UPDATE 
  name = VALUES(name),
  email = 'new@example.com';
```

### 4.3 `REPLACE INTO`
先删除后插入（主键或唯一索引冲突时）

```sql
REPLACE INTO users (id, name) VALUES (1, '新张三');
```

> **有副作用**：实际是 `DELETE` + `INSERT`，会触发删除相关的外键和触发器。

---

## 5. 从其他表插入数据

### `INSERT INTO ... SELECT`：

```sql
INSERT INTO users_backup (id, name)
SELECT id, name FROM users WHERE id < 100;
```

> 常用于批量备份、迁移数据。

---

## 6. 与事务结合使用

```sql
START TRANSACTION;

INSERT INTO orders (id, item) VALUES (101, '商品A');
INSERT INTO order_items (order_id, product) VALUES (101, 'A1');

COMMIT;
```

> 避免插入部分成功、部分失败。

---

## 7. 性能优化建议

| 建议                           | 原因说明                       |
|--------------------------------|-------------------------------|
| 批量插入多行                  | 降低网络往返开销、解析成本    |
| 使用预编译 SQL (`prepare`)    | 减少 SQL 解析开销             |
| 禁用唯一检查（临时导入）     | `SET unique_checks=0;` 提高性能（注意安全性） |
| 关闭自动提交（开启事务）     | `SET autocommit=0;` 减少事务提交频率 |
| 合理使用 `INSERT IGNORE`     | 避免死锁或主键冲突中断批量任务 |

---

## 8. 常见错误

| 错误代码 | 含义说明                     |
|----------|------------------------------|
| 1062     | Duplicate entry 主键冲突     |
| 1048     | Column cannot be null        |
| 1366     | Incorrect value for column   |



---

## 9. 配合函数使用插入

```sql
INSERT INTO logs (create_time, ip)
VALUES (NOW(), INET_ATON('192.168.1.1'));
```

> 可结合时间函数、IP 转换函数、加密函数等。

---

## 10. 检查插入是否成功

```sql
SELECT ROW_COUNT();  -- 返回受影响的行数
```

---



