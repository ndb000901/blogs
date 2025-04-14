# Mysql基础-索引优化

## 1. 建立索引的目的

- **加速查询（SELECT）**
- **加速排序（ORDER BY）**
- **支持唯一性约束（UNIQUE、PRIMARY）**
- **加速关联（JOIN）操作**
- **优化分组查询（GROUP BY）**

---

## 2. 索引设计原则

### 2.1 高选择性优先建立索引

- 选择性 = 不同值个数 / 总记录数
- 选择性越高，索引效果越好

例如：

```sql
SELECT COUNT(DISTINCT sex) FROM users; -- 只有男和女，选择性低
SELECT COUNT(DISTINCT email) FROM users; -- 每人一个邮箱，选择性高
```

### 2.2 使用最左前缀原则

```sql
CREATE INDEX idx_name_age_city ON user(name, age, city);
```

能匹配：

- `WHERE name = ?`
- `WHERE name = ? AND age = ?`
- `WHERE name = ? AND age = ? AND city = ?`

不能匹配：

- `WHERE age = ? AND city = ?` 跳过了 name）

### 2.3 覆盖索引优先级高（避免回表）

如果查询的字段都在索引中，不需要回表，非常高效。

```sql
CREATE INDEX idx_name_age ON user(name, age);

SELECT name, age FROM user WHERE name = 'Tom'; -- 覆盖索引
```

### 2.4 联合索引优于多个单列索引

```sql
-- 优：联合索引
CREATE INDEX idx_name_age ON user(name, age);

-- 不推荐：单列索引
CREATE INDEX idx_name ON user(name);
CREATE INDEX idx_age ON user(age);
```

多个单列索引无法联合使用（不会自动合并）。

---

## 3. 索引选择建议（不同字段类型）

| 字段类型     | 建索引建议             |
|--------------|------------------------|
| 整型字段     | 非常适合做主键或普通索引    |
| 枚举字段     | 选择性太低             |
| 日期字段     | 时间范围查询常见，适合索引   |
| 文本字段     | 前缀索引，或使用全文索引    |
| JSON字段     | 不建议直接索引，需虚拟列+索引 |
| BLOB字段     | 极不适合建索引           |

---

## 4. 常见索引类型的设计技巧

### 4.1 单值索引（唯一索引/主键）

适用于标识唯一记录，例如：

```sql
UNIQUE(email)
PRIMARY KEY(id)
```

### 4.2 联合索引

按照查询常用条件组合设计，结合最左前缀原则。

```sql
CREATE INDEX idx_name_age_status ON user(name, age, status);
```

### 4.3 前缀索引（仅适用于字符串）

针对较长字符串（如邮箱、UUID）只索引部分前缀：

```sql
CREATE INDEX idx_email_prefix ON user(email(10));
```

需通过 `SHOW INDEX FROM user` 观察前缀选择性是否足够。

### 4.4 隐式索引（主键 + 唯一索引自动创建）

```sql
-- 下列语句会自动创建索引
PRIMARY KEY(id)
UNIQUE(email)
```

---

## 5. 避免索引失效的写法

| 错误写法                            | 正确写法                      |
|-------------------------------------|-------------------------------|
| `WHERE age + 1 = 20`               | `WHERE age = 19`              |
| `WHERE LEFT(name, 2) = 'To'`       | 使用 `name` 前缀索引          |
| `WHERE CAST(age AS CHAR) = '20'`  | 避免类型转换                  |
| `WHERE name LIKE '%Tom%'`         | 改用全文索引或前缀 LIKE 查询  |
| `WHERE name = ? OR age = ?`       | 使用 `UNION` 拆分查询         |

---

## 6. 常用索引优化技巧

### 6.1 使用 EXPLAIN 分析 SQL 是否命中索引

```sql
EXPLAIN SELECT * FROM user WHERE name = 'Tom';
```

重点字段：

- `key`：使用的索引名
- `rows`：扫描行数，越小越好
- `type`：访问类型（`ALL`=全表，`ref`=普通索引）

### 6.2 利用 `FORCE INDEX` 强制走某个索引（慎用）

```sql
SELECT * FROM user FORCE INDEX (idx_name) WHERE name = 'Tom';
```

一般只用于分析索引不生效的场景。

### 6.3 分页优化

```sql
-- 慎用
SELECT * FROM user ORDER BY id LIMIT 100000, 10;

-- 推荐（子查询+索引）
SELECT * FROM user 
WHERE id > (
  SELECT id FROM user ORDER BY id LIMIT 100000, 1
)
ORDER BY id LIMIT 10;
```

---

## 7. 索引与事务、锁的关系

- 索引能**缩小锁的范围**（减少锁粒度）
- 没有索引时，InnoDB 会使用**表锁或范围锁**
- 索引失效时，锁范围扩大，影响并发性能

---

## 8. 删除或合并冗余索引

使用工具：

```sql
-- 查看索引
SHOW INDEX FROM user;

-- 删除索引
DROP INDEX idx_name ON user;
```

冗余索引：被覆盖的子集索引。

```sql
-- 这两个索引是冗余的
INDEX(name)
INDEX(name, age)
```

建议保留覆盖字段更多的那个。

---

