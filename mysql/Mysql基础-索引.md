# Mysql基础-索引

## 1. 索引的作用

- 提高 **查询效率**
- 提高 **排序效率**
- 实现 **唯一性约束**
- 加速 **表关联（JOIN）**
- 同时，**写入性能会下降**（插入、更新、删除都要维护索引）

---

## 2. MySQL 索引的分类

### 2.1 按逻辑功能分类

| 类型             | 说明                           | SQL 示例                                 |
|------------------|--------------------------------|------------------------------------------|
| 主键索引         | `PRIMARY KEY`，唯一且非空      | `PRIMARY KEY(id)`                        |
| 唯一索引         | `UNIQUE`，唯一但允许为空       | `UNIQUE(email)`                          |
| 普通索引         | `INDEX`，无唯一性限制          | `INDEX(name)`                            |
| 组合索引         | 多列组成一个索引               | `INDEX(name, age)`                       |
| 前缀索引         | 针对字符串前N个字符建立索引    | `INDEX(name(10))`                        |
| 全文索引         | 适合文本搜索（全文检索）       | `FULLTEXT(name)`                         |
| 空间索引         | 处理空间数据，GIS 相关         | `SPATIAL INDEX(location)`                |

---

### 2.2 按物理结构分类

| 类型             | 引擎          | 数据结构     | 特点                                       |
|------------------|---------------|--------------|--------------------------------------------|
| 聚簇索引         | InnoDB        | B+树         | 主键索引，叶子节点存储整行数据             |
| 辅助索引         | InnoDB        | B+树         | 非主键索引，叶子节点存储主键值             |
| Hash 索引        | Memory 引擎   | 哈希表       | 等值查询快，范围查询慢（仅限 MEMORY 引擎）|
| 全文索引         | InnoDB        | 倒排索引     | 文本搜索使用                              |

---

## 3. 索引创建语法

```sql
-- 普通索引
CREATE INDEX idx_name ON user(name);

-- 唯一索引
CREATE UNIQUE INDEX idx_email ON user(email);

-- 主键索引（建表时定义）
CREATE TABLE user (
  id INT PRIMARY KEY,
  name VARCHAR(50)
);

-- 组合索引
CREATE INDEX idx_name_age ON user(name, age);

-- 前缀索引
CREATE INDEX idx_name_prefix ON user(name(10));
```

---

## 4. 索引结构详解（InnoDB）

### 4.1 聚簇索引（Clustered Index）

- 主键索引
- 叶子节点存储**整行数据**
- 表数据与主键索引是一体的（InnoDB 表=聚簇索引）

### 4.2 辅助索引（Secondary Index）

- 非主键索引
- 叶子节点存储**主键值**，需要**回表**

例如：

```sql
SELECT * FROM user WHERE name = 'Tom';
```

流程：

1. 在 name 辅助索引中查找 name='Tom' → 得到主键 id=1
2. 回表到主键聚簇索引 → 查到整行数据

---

## 5. 最左前缀原则（联合索引核心）

```sql
CREATE INDEX idx_name_age ON user(name, age);
```

索引能被使用的前提是，**WHERE 子句必须包含最左边的字段**。

| 查询条件                 | 能否使用索引      |
|--------------------------|--------------|
| `WHERE name='Tom'`       | 使用(name)    |
| `WHERE name='Tom' AND age=18` | 使用(name, age) |
| `WHERE age=18`           | 无法使用        |

---

## 6. 索引是否生效（查看方式）

使用 `EXPLAIN` 查看 SQL 执行计划：

```sql
EXPLAIN SELECT * FROM user WHERE name = 'Tom';
```

关注字段：

- `key`：使用的索引名
- `rows`：扫描行数，越小越好
- `type`：访问类型（range、ref、const、ALL）

---

## 7. 索引失效场景

| 场景                                     | 原因说明 |
|------------------------------------------|----------|
| `WHERE LEFT(name, 3) = 'Tom'`            | 使用了函数导致索引失效 |
| `WHERE name = 'Tom' OR email = 'x@x.com'`| 多列 OR，索引无法覆盖 |
| `WHERE age + 1 = 18`                     | 表达式计算               |
| 类型不一致（字符串 vs 数字）             | 会发生隐式转换           |
| 使用 `%前缀%` 模糊查询（如 LIKE '%abc'） | 无法使用索引             |

---

## 8. 覆盖索引（Covering Index）

如果查询的字段**都在辅助索引中**，就无需回表，速度极快。

```sql
CREATE INDEX idx_name_age ON user(name, age);

SELECT name, age FROM user WHERE name = 'Tom';
```

此时查询只在 `idx_name_age` 中完成，称为 **覆盖索引**

---

## 9. 索引优化建议

- 尽量用 **选择性高** 的列建立索引
- 联合索引优于多个单列索引
- 切忌滥用索引（维护成本高）
- `LIMIT` 场景可以利用索引加速分页
- 使用合适的 `EXPLAIN` 分析查询性能

---

## 10. 删除与修改索引

```sql
-- 删除索引
DROP INDEX idx_name ON user;

-- 修改索引 = 删除 + 新建
```

---

## 11. 索引下推

索引下推指的是：**在使用索引访问数据时，MySQL 将 WHERE 子句中属于索引字段的判断条件“下推”到存储引擎层进行判断，从而减少回表次数，提升查询效率。**

ICP 是针对 **非覆盖索引场景** 的优化（即，必须回表）。

---

### 11.1 ICP 工作机制

在没有 ICP 时，InnoDB 的执行流程如下：

> **从索引中扫描到主键 → 回表取整行数据 → MySQL Server 层判断 WHERE 条件是否满足 → 不满足则丢弃**

缺点：**即使数据不符合条件，也要回表，IO 开销大**

引入 ICP 后：

> **在存储引擎层用部分 WHERE 条件先做一次过滤（只要条件是索引列） → 满足才回表**

优点：**减少无效回表次数，提高效率**

---

### 11.2 举例说明

假设有如下表结构和索引：

```sql
CREATE TABLE user (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT,
    address VARCHAR(100),
    INDEX idx_name_age (name, age)
);
```

执行如下 SQL：

```sql
SELECT * FROM user 
WHERE name = 'Tom' AND age > 30 AND address LIKE '%Beijing%';
```

- `name` 和 `age` 在索引中，可以在存储引擎层提前过滤（使用 ICP）
- `address` 不在索引中，必须回表后才能判断

通过 ICP：
> 只有 name = 'Tom' 且 age > 30 的记录才回表，再判断 address。

可以用 `EXPLAIN` 验证是否使用了 ICP：

```sql
EXPLAIN SELECT * FROM user 
WHERE name = 'Tom' AND age > 30 AND address LIKE '%Beijing%';
```

查看字段：

- `Extra` 中会出现：`Using index condition` 表示使用了 ICP
- 没有使用时可能只显示 `Using where; Using index`

---

### 11.3 启用/禁用 ICP

ICP 默认是开启的，可以手动控制：

```sql
SET optimizer_switch='index_condition_pushdown=off'; -- 关闭

SET optimizer_switch='index_condition_pushdown=on';  -- 开启
```

---

### 11.4 什么时候 ICP 效果明显

- 表数据量大，且回表成本高时
- 索引中多个字段参与 WHERE 条件过滤
- WHERE 子句中只有部分字段在索引中，不能形成覆盖索引

---


