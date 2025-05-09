# Mysql基础-数据行结构


## 1. InnoDB 数据行结构总览

每一行记录，InnoDB 都会存储三部分信息：

```
┌───────────────────────────┐
│   1. 变长字段长度列表     │
│   2. NULL 值标志位         │
│   3. 实际字段值（用户+系统） │
└───────────────────────────┘
```

---

## 2. 用户可见字段部分

比如你建表如下：

```sql
CREATE TABLE user (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  age INT
) ENGINE=InnoDB;
```

InnoDB 行中会包含这些**用户定义字段**：

| 字段名 | 是否变长 | 是否允许 NULL |
|--------|----------|---------------|
| id     | 否       | 否            |
| name   | 是       | 是            |
| age    | 否       | 是            |

---

## 3. 系统隐藏字段（3个）

InnoDB 在每行中**自动添加三个隐藏字段**：

| 字段名 | 说明 |
|--------|------|
| **DB_TRX_ID** | 最后一次修改该行的事务 ID（6 字节） |
| **DB_ROLL_PTR** | 指向 Undo 日志的指针（7 字节） |
| **DB_ROW_ID** | 隐藏主键（6 字节，仅在没有显式主键时才有） |

如果表有显式主键（如 `id`），那就不再使用 `DB_ROW_ID`。

---

## 4. 完整行记录结构（物理页中的格式）

在页（Page）中，每一行的存储结构大致如下：

```
┌──────────────┐
│ Header（5B）  │  → 标识标志、删除位等
├──────────────┤
│ 变长字段长度   │  → 每个变长字段的长度（如 varchar）
├──────────────┤
│ NULL 标志位   │  → 表示哪些字段为 NULL
├──────────────┤
│ 用户字段值     │
├──────────────┤
│ DB_TRX_ID    │
├──────────────┤
│ DB_ROLL_PTR  │
├──────────────┤
│ 主键或DB_ROW_ID│
└──────────────┘
```

---





