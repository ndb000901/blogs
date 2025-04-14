# Mysql基础-触发器

MySQL **触发器（Trigger）** 是一种特殊类型的存储程序，它会在特定事件（如 `INSERT`、`UPDATE`、`DELETE`）发生时自动执行。触发器可以在表的某些操作前或后触发，从而实现一些自动化的操作或数据一致性检查。

## 1. 触发器的基本概念

触发器是与表相关联的，它可以在某些特定操作发生时自动执行。可以在以下几种情况触发触发器：
- 在执行 `INSERT`、`UPDATE` 或 `DELETE` 操作时。
- 在操作发生之前（`BEFORE`）或操作发生之后（`AFTER`）。

触发器通常用于：
- 自动验证数据的一致性。
- 自动更新审计日志。
- 进行自动化数据修改。

---

## 2. 触发器的基本语法

在 MySQL 中，触发器的创建使用 `CREATE TRIGGER` 语句。语法格式如下：

```sql
CREATE TRIGGER trigger_name
    trigger_time trigger_event
    ON table_name
    FOR EACH ROW
    trigger_body;
```

- **`trigger_name`**：触发器的名称。
- **`trigger_time`**：触发器的触发时间，`BEFORE` 或 `AFTER`。
- **`trigger_event`**：触发器响应的事件，`INSERT`、`UPDATE` 或 `DELETE`。
- **`table_name`**：触发器作用的表名。
- **`trigger_body`**：触发器执行的操作。

### 示例：

1. **创建一个简单的触发器：**

```sql
CREATE TRIGGER before_insert_example
    BEFORE INSERT
    ON users
    FOR EACH ROW
    BEGIN
        SET NEW.created_at = NOW();
    END;
```

- 这个触发器将在 `users` 表插入数据之前触发，自动为每一行数据填充 `created_at` 字段的当前时间。

2. **创建一个 `AFTER UPDATE` 触发器：**

```sql
CREATE TRIGGER after_update_example
    AFTER UPDATE
    ON users
    FOR EACH ROW
    BEGIN
        INSERT INTO logs (user_id, change_time, action)
        VALUES (NEW.id, NOW(), 'UPDATE');
    END;
```

- 这个触发器将在 `users` 表的数据更新之后触发，记录一条日志。

---

## 3. 触发器的触发时间（`BEFORE` 和 `AFTER`）

1. **`BEFORE`**：
    - 当指定为 `BEFORE` 时，触发器会在数据库执行实际操作（如 `INSERT`、`UPDATE`、`DELETE`）之前触发。
    - 可以在此时修改即将插入或更新的数据。
    - 示例：
      ```sql
      CREATE TRIGGER before_insert_example
          BEFORE INSERT
          ON users
          FOR EACH ROW
          BEGIN
              SET NEW.created_at = NOW();  -- 插入数据前，自动填充时间
          END;
      ```

2. **`AFTER`**：
    - 当指定为 `AFTER` 时，触发器会在数据库执行实际操作之后触发。
    - 通常用于记录日志、审计或进行额外的操作。
    - 示例：
      ```sql
      CREATE TRIGGER after_update_example
          AFTER UPDATE
          ON users
          FOR EACH ROW
          BEGIN
              INSERT INTO logs (user_id, action, change_time)
              VALUES (NEW.id, 'UPDATE', NOW());
          END;
      ```

---

## 4. 触发器中的 `NEW` 和 `OLD`

- **`NEW`**：用于访问在 `INSERT` 或 `UPDATE` 操作中的新数据行。在 `INSERT` 中，`NEW` 表示即将插入的数据行。在 `UPDATE` 中，`NEW` 表示更新后的数据行。

  示例：
  ```sql
  SET NEW.created_at = NOW();
  ```
  这会在 `INSERT` 或 `UPDATE` 操作时将 `created_at` 字段设置为当前时间。

- **`OLD`**：用于访问在 `UPDATE` 或 `DELETE` 操作中的旧数据行。在 `UPDATE` 中，`OLD` 表示更新前的数据行。在 `DELETE` 中，`OLD` 表示将要删除的数据行。

  示例：
  ```sql
  INSERT INTO logs (action, old_value)
  VALUES ('DELETE', OLD.value);
  ```
  这会记录被删除的数据。

---

## 5. 触发器的使用示例

1. **数据完整性检查：**
   触发器可以用于验证数据的合法性，确保数据的一致性。

   示例：
   ```sql
   CREATE TRIGGER check_salary_before_insert
       BEFORE INSERT
       ON employees
       FOR EACH ROW
       BEGIN
           IF NEW.salary < 3000 THEN
               SIGNAL SQLSTATE '45000'
               SET MESSAGE_TEXT = 'Salary must be at least 3000';
           END IF;
       END;
   ```

    - 在此示例中，如果 `salary` 小于 3000，插入操作将失败。

2. **自动审计日志：**
   可以使用触发器记录数据修改的日志信息。

   示例：
   ```sql
   CREATE TRIGGER after_insert_employee
       AFTER INSERT
       ON employees
       FOR EACH ROW
       BEGIN
           INSERT INTO audit_log (action, table_name, changed_data)
           VALUES ('INSERT', 'employees', CONCAT('ID:', NEW.id, ', Name:', NEW.name));
       END;
   ```

    - 这会在插入 `employees` 表的数据后，自动插入一条审计日志。

3. **自动更新时间戳：**
   触发器可以自动为记录添加或更新时间戳。

   示例：
   ```sql
   CREATE TRIGGER before_update_timestamp
       BEFORE UPDATE
       ON orders
       FOR EACH ROW
       BEGIN
           SET NEW.updated_at = NOW();
       END;
   ```

    - 这会在更新 `orders` 表中的记录时，自动更新 `updated_at` 字段为当前时间。

---

## 6. 触发器的管理

1. **查看所有触发器：**

   可以通过以下 SQL 查询来查看当前数据库中的所有触发器：
   ```sql
   SHOW TRIGGERS;
   ```

2. **删除触发器：**

   可以使用 `DROP TRIGGER` 删除已存在的触发器：

   ```sql
   DROP TRIGGER [IF EXISTS] trigger_name;
   ```

   示例：
   ```sql
   DROP TRIGGER IF EXISTS after_update_example;
   ```

---

## 7. 触发器的限制和注意事项

1. **触发器的递归调用：**
   MySQL 不允许触发器在执行过程中递归调用自己。如果触发器本身在执行时对表进行修改，会导致递归触发。因此，要谨慎使用触发器避免递归循环。

2. **性能考虑：**
   触发器虽然在操作之前或之后自动执行，但大量触发器的使用可能会导致性能问题。尤其是在表的数据量较大时，触发器的执行可能会增加查询和更新操作的开销。

3. **触发器的错误处理：**
   如果触发器内的代码执行出错，它会导致原始操作失败并回滚。因此，在编写触发器时，要特别注意错误处理和调试。

---
