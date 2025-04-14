# Mysql基础-虚拟列

## 1. 虚拟列（Generated Column）

虚拟列是：

> 表中 **不直接存储值**，而是根据其他列通过表达式**动态计算**得到的列。

在 `SELECT`、`WHERE`、`ORDER BY` 等操作中可以像普通列一样使用。

---

## 2. 分类：虚拟 vs 持久化（STORED）

| 类型       | 说明                             |
|------------|----------------------------------|
| **虚拟（VIRTUAL）** | 默认类型，**不存储数据**，使用时实时计算 |
| **持久化（STORED）** | 显式声明，**存储数据**，插入/更新时计算 |

示例对比：

```sql
-- 虚拟列（默认）
ALTER TABLE users ADD full_name VARCHAR(200) GENERATED ALWAYS AS (CONCAT(first_name, ' ', last_name));

-- 持久化列
ALTER TABLE users ADD full_name VARCHAR(200) GENERATED ALWAYS AS (CONCAT(first_name, ' ', last_name)) STORED;
```

---

## 3. 语法说明

```sql
column_name [data_type] GENERATED ALWAYS AS (expression) [VIRTUAL | STORED] [UNIQUE | NOT NULL]
```

- `expression`：支持任意**确定性表达式**
- `VIRTUAL`：默认值（可省略）
- `STORED`：需要存储结果时使用
- 可添加索引（STORED 支持全文索引）

---

## 4. 使用示例

### 4.1 虚拟列：自动生成身份证前6位（省份代码）

```sql
CREATE TABLE citizens (
    id_card CHAR(18),
    province_code CHAR(6) GENERATED ALWAYS AS (LEFT(id_card, 6)) VIRTUAL
);
```

### 4.2 持久化列：存储 BMI 指数

```sql
CREATE TABLE health_data (
    height_cm DECIMAL(5,2),
    weight_kg DECIMAL(5,2),
    bmi DECIMAL(5,2) GENERATED ALWAYS AS (weight_kg / POW(height_cm/100, 2)) STORED
);
```

---

## 5. 限制与注意事项

| 限制项                          | 描述                                               |
|----------------------------------|----------------------------------------------------|
| 不能包含子查询、变量、UDF 等     | 必须是 **确定性表达式**                             |
| 不能引用其他虚拟列（嵌套）      | 要引用，只能引用**非虚拟列**                        |
| VIRTUAL 列不能建立 FULLTEXT/GIS 索引 | 必须使用 STORED 才能支持                           |
| 不支持更新虚拟列值              | 只能通过源字段更新，不能对虚拟列直接赋值          |

---

## 6. 与触发器/视图/计算列的对比

| 特性             | 虚拟列                 | 视图                        | 触发器                |
|------------------|---------------------|----------------------------|----------------------|
| 自动更新         | 是                   | 否（变更源数据不会自动更新）   | 通过代码维护            |
| 存储数据         | 否（VIRTUAL） / 是（STORED） |  否                        | 可更新真实字段           |
| 支持索引         | （STORED 可 FULLTEXT） |                            |                     |
| 使用复杂逻辑     | 受限                  | 可用子查询、JOIN 等          | 可编程                |
| 性能影响         | 轻（VIRTUAL）/重（STORED） | 中等（每次查视图都执行）       | 重（需手动维护）          |

---

## 7. 索引支持

| 类型     | 支持索引 | 可作为主键/唯一键 |
|----------|----------|-----------|
| VIRTUAL  | （支持二级索引） | 可         |
| STORED   | （支持全文/GIS） | 可         |

示例：

```sql
ALTER TABLE citizens ADD INDEX idx_province_code (province_code);
```

---

## 8. 查询示例

```sql
-- 插入数据
INSERT INTO citizens (id_card) VALUES ('440101199001011234');

-- 查询生成列
SELECT id_card, province_code FROM citizens;

-- 更新源字段，自动影响虚拟列
UPDATE citizens SET id_card = '330102198501017654' WHERE id = 1;
```

---


