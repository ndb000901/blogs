# Mysql基础-储存过程


## 1. 存储过程

> 存储过程是一组预编译 SQL 语句的集合，存储在数据库中，可通过调用一次性执行，适用于封装业务逻辑、简化代码、提高重用性和性能。

---

## 2. 创建语法

```sql
DELIMITER $$

CREATE PROCEDURE procedure_name(
    IN param1 INT,
    OUT param2 VARCHAR(100),
    INOUT param3 DATE
)
BEGIN
    -- SQL语句块
END$$

DELIMITER ;
```

### 参数类型说明：

| 类型   | 说明                         |
|--------|------------------------------|
| `IN`   | 传入参数（默认）             |
| `OUT`  | 输出参数                     |
| `INOUT`| 输入并输出参数               |

---

## 3. 调用存储过程

```sql
CALL procedure_name(参数1, @out_param, @inout_param);
```

查看输出参数结果：

```sql
SELECT @out_param, @inout_param;
```

---

## 4. 示例：加法存储过程

```sql
DELIMITER $$

CREATE PROCEDURE add_numbers(IN a INT, IN b INT, OUT sum INT)
BEGIN
    SET sum = a + b;
END$$

DELIMITER ;

-- 调用
CALL add_numbers(3, 5, @result);
SELECT @result;
```

---

## 5. 流程控制语法

### 5.1 条件语句

```sql
IF 条件 THEN
    -- 操作
ELSEIF 条件 THEN
    -- 操作
ELSE
    -- 操作
END IF;
```

### 5.2 循环语句

#### WHILE 循环

```sql
WHILE 条件 DO
    -- 循环内容
END WHILE;
```

#### LOOP（配合 LEAVE）

```sql
label: LOOP
    IF 条件 THEN
        LEAVE label;
    END IF;
END LOOP;
```

#### REPEAT

```sql
REPEAT
    -- 语句
UNTIL 条件成立
END REPEAT;
```

---

## 6. 局部变量定义

```sql
DECLARE var_name INT DEFAULT 0;
```

### 赋值方式：

```sql
SET var_name = 100;
SELECT 字段 INTO var_name FROM 表 WHERE 条件;
```

---

## 7. 游标（CURSOR）处理多行数据

```sql
DECLARE done INT DEFAULT 0;
DECLARE cur CURSOR FOR SELECT id FROM users;
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

OPEN cur;
read_loop: LOOP
    FETCH cur INTO @uid;
    IF done THEN
        LEAVE read_loop;
    END IF;
    -- 对 @uid 做处理
END LOOP;
CLOSE cur;
```

---

## 8. 查看/删除存储过程

```sql
-- 查看当前库所有存储过程
SHOW PROCEDURE STATUS WHERE Db = 'your_db';

-- 查看过程体
SHOW CREATE PROCEDURE procedure_name;

-- 删除存储过程
DROP PROCEDURE procedure_name;
```

---

## 9. 调试技巧

MySQL 没有内置调试器，可用以下方式调试：

- 打印日志表记录执行状态
- 设置中间变量并 `SELECT`
- 使用 `SIGNAL SQLSTATE` 抛出异常

```sql
SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = '自定义错误';
```

---

## 10. 权限要求

要创建存储过程，用户需具备：

```sql
CREATE ROUTINE, ALTER ROUTINE, EXECUTE
```

授权示例：

```sql
GRANT CREATE ROUTINE, EXECUTE ON mydb.* TO 'user'@'localhost';
```

---

## 11. 函数 vs 存储过程 vs 触发器对比

| 特性            | 函数 Function       | 存储过程 Procedure      | 触发器 Trigger         |
|-----------------|----------------------|------------------------|-------------------------|
| 是否返回值      | 有返回值              | 可选输出参数             | 无直接返回值            |
| 调用方式        | 可嵌入 SQL           | 通过 `CALL`            | 自动触发（DML）         |
| 可否写事务      | 不允许               | 允许                   | 可控制，但推荐简单逻辑  |
| 适用场景        | 计算、转换             | 批量操作、复杂流程       | 数据变更自动处理        |

---

## 12. 存储过程的优点

- 预编译：首次执行后缓存执行计划
- 封装业务逻辑：简化客户端代码
- 提高安全性：限制数据访问路径
- 可重用、易维护

---
