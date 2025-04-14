# Mysql基础-流程控制


## 1. IF 语句

`IF` 语句用于执行条件判断，它允许我们根据条件执行不同的 SQL 语句。

### 语法

```sql
IF condition THEN
    -- 执行 SQL 语句
ELSEIF condition THEN
    -- 执行其他 SQL 语句
ELSE
    -- 执行其他 SQL 语句
END IF;
```

- **`condition`**：可以是任何返回布尔值（`TRUE` 或 `FALSE`）的表达式。
- **`THEN`**：当条件为真时，执行接下来的 SQL 语句。

### 示例

```sql
DELIMITER $$

CREATE PROCEDURE check_salary (IN salary DECIMAL)
BEGIN
    IF salary < 2000 THEN
        SELECT 'Salary is too low';
    ELSEIF salary BETWEEN 2000 AND 5000 THEN
        SELECT 'Salary is average';
    ELSE
        SELECT 'Salary is high';
    END IF;
END $$

DELIMITER ;
```

---

## 2. CASE 语句

`CASE` 语句用于在多个条件中选择一个，类似于其他编程语言中的 `switch` 语句。可以用于更复杂的条件判断。

### 语法

```sql
CASE
    WHEN condition THEN result
    WHEN condition THEN result
    ELSE result
END
```

- `WHEN condition`：如果条件成立，则返回后面的 `result`。
- `ELSE`：如果没有条件成立，返回 `ELSE` 后面的结果。

### 示例

```sql
SELECT 
    id, 
    salary,
    CASE
        WHEN salary < 2000 THEN 'Low'
        WHEN salary BETWEEN 2000 AND 5000 THEN 'Average'
        ELSE 'High'
    END AS salary_category
FROM employees;
```

---

## 3. LOOP 语句

`LOOP` 是 MySQL 中的循环语句，能够无限次执行循环中的 SQL 语句，直到遇到 `LEAVE` 语句。

### 语法

```sql
LOOP
    -- 循环语句
    LEAVE loop_name;  -- 退出循环
END LOOP;
```

- `LEAVE loop_name;` 用来跳出循环，结束循环执行。

### 示例

```sql
DELIMITER $$

CREATE PROCEDURE loop_example()
BEGIN
    DECLARE counter INT DEFAULT 1;
    loop_start:
    LOOP
        IF counter > 5 THEN
            LEAVE loop_start;  -- 当条件满足时退出循环
        END IF;
        SELECT counter;
        SET counter = counter + 1;
    END LOOP;
END $$

DELIMITER ;
```

---

## 4. WHILE 语句

`WHILE` 语句是基于条件的循环控制结构，它会在每次执行之前检查条件是否成立。

### 语法

```sql
WHILE condition DO
    -- 循环语句
END WHILE;
```

- `condition` 是一个返回布尔值的表达式。如果为 `TRUE`，循环体中的 SQL 语句会执行。

### 示例

```sql
DELIMITER $$

CREATE PROCEDURE while_example()
BEGIN
    DECLARE counter INT DEFAULT 1;
    WHILE counter <= 5 DO
        SELECT counter;
        SET counter = counter + 1;
    END WHILE;
END $$

DELIMITER ;
```

---

## 5. REPEAT 语句

`REPEAT` 语句与 `WHILE` 相似，区别在于它首先执行一次循环体，然后检查条件。只要条件为 `FALSE`，循环就会继续执行。

### 语法

```sql
REPEAT
    -- 循环语句
UNTIL condition END REPEAT;
```

- `condition` 是循环停止的条件，如果为 `TRUE`，则退出循环。

### 示例

```sql
DELIMITER $$

CREATE PROCEDURE repeat_example()
BEGIN
    DECLARE counter INT DEFAULT 1;
    REPEAT
        SELECT counter;
        SET counter = counter + 1;
    UNTIL counter > 5 END REPEAT;
END $$

DELIMITER ;
```

---

## 6. LEAVE 与 ITERATE

在 `LOOP`、`WHILE` 和 `REPEAT` 循环中，我们可以使用 `LEAVE` 和 `ITERATE` 来控制流程。

- `LEAVE` 用于退出循环。
- `ITERATE` 用于跳过当前循环的剩余部分，直接开始下一次循环。

### 示例

```sql
DELIMITER $$

CREATE PROCEDURE leave_iterate_example()
BEGIN
    DECLARE counter INT DEFAULT 1;
    loop_start:
    LOOP
        IF counter = 3 THEN
            ITERATE loop_start;  -- 跳过当前循环
        END IF;
        IF counter > 5 THEN
            LEAVE loop_start;  -- 退出循环
        END IF;
        SELECT counter;
        SET counter = counter + 1;
    END LOOP;
END $$

DELIMITER ;
```

---

## 7. HANDLER 语句

`HANDLER` 语句用于处理异常。它用于在 MySQL 存储过程中捕获并处理错误或特定条件。

### 语法

```sql
DECLARE CONTINUE HANDLER FOR SQLSTATE 'VALUE' statement;
DECLARE EXIT HANDLER FOR SQLSTATE 'VALUE' statement;
```

- `CONTINUE`：在捕获到错误时，继续执行接下来的语句。
- `EXIT`：捕获错误后退出当前存储过程或程序块。

### 示例

```sql
DELIMITER $$

CREATE PROCEDURE handler_example()
BEGIN
    DECLARE CONTINUE HANDLER FOR SQLEXCEPTION
        SELECT 'An error occurred';
    -- 错误操作
    INSERT INTO nonexistent_table (col1) VALUES (1);
    SELECT 'No error';
END $$

DELIMITER ;
```

---
