# SQL注入

SQL 注入（SQL Injection, SQLi）是 Web 安全中最经典、危害最大的漏洞之一。它本质是攻击者将恶意 SQL 代码注入到应用的查询中，从而**非法访问或操作数据库**。


---

## 1. SQL 注入的本质

**核心思想**：攻击者构造恶意 SQL 语句片段，通过输入点插入原本不应执行的 SQL 命令，使数据库执行攻击者意图的操作。

**例如（伪代码）**：

```sql
SELECT * FROM users WHERE username = '$input' AND password = '$pass';
```

如果用户输入为：

- `username = 'admin'`
- `password = 'anything' OR '1'='1'`

最终拼接为：

```sql
SELECT * FROM users WHERE username = 'admin' AND password = 'anything' OR '1'='1';
```

`OR '1'='1'` 恒为真 → 绕过认证。

---

## 2. 常见 SQL 注入类型

### 2.1 **基于错误（Error-Based）**

利用 SQL 报错信息获取数据库结构信息。

**例子**：
```sql
?id=1' AND updatexml(1,concat(0x7e,database()),1)--+
```

如果数据库返回错误信息，就能泄露数据库名。

---

### 2.2 **联合查询注入（UNION-Based）**

利用 `UNION` 合并另一个 SQL 查询结果。

```sql
?id=1' UNION SELECT null, username, password FROM users--+
```

前提是知道查询字段数量。

---

### 2.3 **布尔盲注（Boolean-Based Blind）**

不返回数据内容，但可根据响应页面的变化判断真假。

```sql
?id=1' AND 1=1--+  → 正常页面  
?id=1' AND 1=2--+  → 空白或错误页
```

通过这种方式逐位爆破数据库名、表名、列名和数据。

---

### 2.4 **时间盲注（Time-Based Blind）**

数据库执行延时函数来判断真假。

```sql
?id=1' AND IF(SUBSTRING(user(),1,1)='r', SLEEP(5), 0)--+
```

如果延迟 5 秒，说明猜对了，逐位爆破。

---

### 2.5 **堆叠注入（Stacked Queries）**

支持多条 SQL 语句一起执行（部分数据库支持，比如 MSSQL、PostgreSQL）。

```sql
?id=1'; DROP TABLE users;--+
```

大多数现代 MySQL 的 web 应用不支持多语句执行（默认关闭）。

---

### 2.6 **二次注入（Second-Order Injection）**

注入的 payload 在第一次提交时不会立即执行，而是在后续某次查询中触发。

---

## 3. SQL 注入利用工具

| 工具        | 用途说明                      |
|-------------|-------------------------------|
| **sqlmap**  | 自动化注入检测与利用           |
| **BurpSuite** | 拦截/重放请求，配合 sqlmap 使用 |
| **Havij**    | GUI 注入工具（偏古早）        |
| **sqlninja**| 专注于 MSSQL 注入              |

---

## 4. SQL 注入的防御方法

### 4.1 **使用预处理语句（Prepared Statements）**

```php
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ? AND password = ?");
$stmt->execute([$username, $password]);
```

防止注入的根本方法，不拼接 SQL。

---

### 4.2 **ORM 框架**

如：MyBatis、Hibernate、SQLAlchemy 等，封装查询语句，也减少注入风险。

---

### 4.3 **输入过滤与白名单验证**

- 仅允许合法字符
- 过滤特殊字符（如 `'`, `"`, `;`, `--`）
- 正则匹配用户名/邮箱等格式

> 注意：过滤只能作为辅助，核心还是参数化查询！

---

### 4.4 **关闭不必要的数据库报错**

防止错误信息泄露结构。


---

### 4.5 **权限最小化**

数据库用户仅授予需要的权限，比如 Web 用户禁止执行 `DROP` 或 `UPDATE` 全库权限。

---

## 5. 手工注入技巧

- 判断注入点：输入 `'` 看是否报错
- 判断列数：使用 `ORDER BY N` 测试（直到出错）
- 联合注入测试：`UNION SELECT 1,2,3...`
- 利用 `information_schema` 枚举表：
    ```sql
    SELECT table_name FROM information_schema.tables WHERE table_schema=database();
    ```

---
