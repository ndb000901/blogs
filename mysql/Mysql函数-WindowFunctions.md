# Mysql函数-WindowFunctions

在 MySQL 8.0 中，**Window Functions**（窗口函数）提供了强大的分析功能，允许在结果集的行之间进行复杂的计算。窗口函数不像聚合函数那样汇总数据，它们会保留行的详细信息，并允许对每一行基于某些条件执行计算。

## 1. 窗口函数

窗口函数是 SQL 中的一类特殊函数，能够在某个“窗口”内执行计算，这个窗口由 SQL 查询中的 `OVER` 子句来定义。与普通聚合函数不同，窗口函数对每一行计算而不需要分组数据，因此每一行都会保留。

---

## 2.窗口函数的基本结构

窗口函数的基本语法：

```sql
<window_function> OVER ([PARTITION BY partition_expr] [ORDER BY order_expr] [ROWS window_frame])
```

- `window_function`: 窗口函数，例如 `ROW_NUMBER()`, `RANK()`, `LEAD()`, `LAG()` 等。
- `PARTITION BY`: 按照某个列的值将结果集分成多个分区，类似于 `GROUP BY`，但不会对行进行合并。
- `ORDER BY`: 定义在分区内的行排序方式。
- `ROWS` 或 `RANGE`: 用于指定窗口的范围，决定当前行及其周围的行。

---

## 3. 常见的窗口函数

### 3.1 `ROW_NUMBER()`
为每一行分配一个唯一的顺序编号。
```sql
SELECT name, salary, ROW_NUMBER() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

- 结果会按 `salary` 从高到低排序，并为每行分配一个唯一的行号（`rank`）。

### 3.2 `RANK()`
计算排名，如果出现相同的值，它们会共享同一排名，之后的排名会跳过。

```sql
SELECT name, salary, RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

- 如果两个员工的工资相同，他们将共享排名 1，接下来的是排名 3（跳过了 2）。

### 3.3 `DENSE_RANK()`
与 `RANK()` 类似，但不会跳过排名。如果有多个相同的值，它们会共享相同的排名，但下一个排名会紧接着上一个排名。

```sql
SELECT name, salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

- 如果两个员工的工资相同，他们会共享排名 1，接下来的是排名 2。

### 3.4 `NTILE(n)`
将结果集分成 `n` 等份并返回每行所在的分区号。

```sql
SELECT name, salary, NTILE(4) OVER (ORDER BY salary DESC) AS quartile
FROM employees;
```

- 这个查询将所有员工的工资分成 4 份，返回每个员工所属的分区（1 到 4）。

### 3.5 `LEAD()` 和 `LAG()`
`LEAD()` 用于访问当前行之后的某一行的数据，`LAG()` 用于访问当前行之前的某一行的数据。

```sql
SELECT name, salary,
       LEAD(salary, 1) OVER (ORDER BY salary DESC) AS next_salary,
       LAG(salary, 1) OVER (ORDER BY salary DESC) AS prev_salary
FROM employees;
```

- `LEAD(salary, 1)` 返回当前行之后一行的 `salary` 值，`LAG(salary, 1)` 返回当前行之前一行的 `salary` 值。

### 3.6 `FIRST_VALUE()` 和 `LAST_VALUE()`
分别返回窗口内第一行和最后一行的值。

```sql
SELECT name, salary,
       FIRST_VALUE(salary) OVER (PARTITION BY department ORDER BY salary DESC) AS highest_salary
FROM employees;
```

- 这个查询返回每个部门中工资最高的员工的工资。

### 3.7 `SUM()`, `AVG()`, `MIN()`, `MAX()`, `COUNT()`
这些聚合函数可以作为窗口函数使用，在窗口中对数据执行累加、平均值等操作。

```sql
SELECT name, salary,
       SUM(salary) OVER (PARTITION BY department ORDER BY salary DESC) AS department_salary_sum
FROM employees;
```

- 这个查询对每个部门的员工薪水进行累加。

---

## 4. 窗口函数的高级用法

### 4.1 使用 `PARTITION BY` 划分窗口
`PARTITION BY` 子句将查询结果分成不同的分区，窗口函数将在每个分区内进行计算。

```sql
SELECT name, salary, department,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;
```

- 这个查询为每个部门中的员工按工资降序排名。

### 4.2 窗口大小的控制 `ROWS` 和 `RANGE`
`ROWS` 和 `RANGE` 子句定义了窗口的范围。`ROWS` 用于指定基于行的窗口大小，`RANGE` 用于基于值的窗口大小。

```sql
SELECT name, salary, 
       SUM(salary) OVER (PARTITION BY department ORDER BY salary ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumulative_salary
FROM employees;
```

- 这个查询为每个部门计算员工的累计薪资。

### 4.3 使用 `ROWS BETWEEN`
`ROWS BETWEEN` 允许定义窗口的起始和结束位置：

- `UNBOUNDED PRECEDING`：窗口开始于分区中的第一行。
- `CURRENT ROW`：窗口结束于当前行。
- `n PRECEDING`：窗口开始于当前行之前的 n 行。
- `n FOLLOWING`：窗口结束于当前行之后的 n 行。

```sql
SELECT name, salary, 
       SUM(salary) OVER (PARTITION BY department ORDER BY salary ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS rolling_sum
FROM employees;
```

- 这个查询为每个员工计算一个滚动薪资总和，窗口包含当前行及其前后各一行。

---



