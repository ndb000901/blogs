# Mysql基础-元数据

**元数据（Metadata）** 是描述数据库对象（如表、列、索引、约束、视图、存储过程等）数据的“数据”，即关于数据的数据。MySQL 中的元数据包含了有关数据库结构、对象、权限等的信息，通常用于管理和查询数据库对象的特性和状态。

MySQL 提供了几种方式来查看和操作数据库的元数据，主要是通过 `INFORMATION_SCHEMA` 数据库和一些特定的系统表。

## 1. INFORMATION_SCHEMA 数据库

`INFORMATION_SCHEMA` 是一个系统数据库，用于存储关于 MySQL 数据库的元数据。它提供了多张表来查询数据库的结构、对象、权限、存储过程等信息。用户可以通过查询这些表来获取数据库的状态和配置。

### **常用表：**

#### 1. **TABLES**：包含了所有数据库的表信息。
- **`TABLES`** 表包含了所有数据库的表的元数据，包括表名、表的类型、创建时间、表的引擎等信息。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'your_database';
   ```

  - 主要字段：
      - `TABLE_NAME`：表的名称
      - `TABLE_SCHEMA`：表所属的数据库
      - `TABLE_TYPE`：表的类型（例如，`BASE TABLE` 表示基础表，`VIEW` 表示视图）
      - `ENGINE`：表使用的存储引擎（如 `InnoDB`、`MyISAM` 等）
      - `CREATE_TIME`：表的创建时间

2. **COLUMNS**：包含了表中所有列的信息。
    - **`COLUMNS`** 表提供了关于每个表的每一列的元数据，包括列的名称、数据类型、默认值、是否允许 NULL、索引等。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_SCHEMA = 'your_database' AND TABLE_NAME = 'your_table';
   ```

    - 主要字段：
        - `COLUMN_NAME`：列名
        - `DATA_TYPE`：列的数据类型（如 `VARCHAR`、`INT`、`DATE` 等）
        - `IS_NULLABLE`：是否允许 `NULL`
        - `COLUMN_DEFAULT`：列的默认值
        - `CHARACTER_MAXIMUM_LENGTH`：最大字符长度（针对字符串类型的列）
        - `NUMERIC_PRECISION`：数字精度（针对数值类型的列）

3. **KEY_COLUMN_USAGE**：提供了关于表中键列（主键、外键等）的信息。
    - 该表存储了与索引相关的元数据，包括哪些列属于哪个索引、索引的类型等。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE WHERE TABLE_SCHEMA = 'your_database' AND TABLE_NAME = 'your_table';
   ```

    - 主要字段：
        - `CONSTRAINT_NAME`：约束的名称（例如主键、外键）
        - `COLUMN_NAME`：列名
        - `TABLE_NAME`：表名
        - `REFERENCED_TABLE_NAME`：如果是外键，还会列出引用的表名

4. **STATISTICS**：关于表的索引信息。
    - **`STATISTICS`** 表包含了索引的元数据，例如索引名称、唯一性、索引类型、索引中包含的列等。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.STATISTICS WHERE TABLE_SCHEMA = 'your_database' AND TABLE_NAME = 'your_table';
   ```

    - 主要字段：
        - `INDEX_NAME`：索引的名称
        - `COLUMN_NAME`：索引中的列名
        - `NON_UNIQUE`：索引是否唯一（0 表示唯一，1 表示非唯一）
        - `SEQ_IN_INDEX`：列在索引中的顺序

5. **USER_PRIVILEGES**：提供了数据库用户权限的元数据。
    - **`USER_PRIVILEGES`** 表包含了关于用户在各个数据库中的权限信息。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.USER_PRIVILEGES WHERE GRANTEE = 'user@localhost';
   ```

    - 主要字段：
        - `GRANTEE`：用户和主机的标识（例如 `user@localhost`）
        - `TABLE_CATALOG`：权限来源的目录
        - `PRIVILEGE_TYPE`：权限类型（例如 `SELECT`、`INSERT`、`UPDATE` 等）
        - `IS_GRANTABLE`：是否可以将权限授予他人

6. **ROUTINES**：关于存储过程和存储函数的元数据。
    - **`ROUTINES`** 表提供了关于存储过程和函数的定义信息。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'your_database';
   ```

    - 主要字段：
        - `ROUTINE_NAME`：存储过程或函数的名称
        - `ROUTINE_TYPE`：类型（例如 `PROCEDURE` 或 `FUNCTION`）
        - `DATA_TYPE`：返回数据类型（仅存储函数有）
        - `ROUTINE_DEFINITION`：存储过程或函数的定义（取决于 MySQL 配置，某些配置下会返回定义内容）

7. **VIEWS**：提供视图的元数据。
    - **`VIEWS`** 表包含了视图的元数据，包括视图的名称、定义等。

   ```sql
   SELECT * FROM INFORMATION_SCHEMA.VIEWS WHERE TABLE_SCHEMA = 'your_database';
   ```

    - 主要字段：
        - `TABLE_NAME`：视图的名称
        - `VIEW_DEFINITION`：视图的定义（SQL 查询）

---

## 2. 查询元数据示例

### 2.1 **查询所有数据库的信息：**

```sql
SELECT * FROM INFORMATION_SCHEMA.SCHEMATA;
```

返回数据库的名称、字符集等信息。

### 2.2 **查询数据库中所有表的信息：**

```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'your_database';
```

### 2.3 **查询表中所有列的定义：**

```sql
SELECT COLUMN_NAME, DATA_TYPE, IS_NULLABLE, COLUMN_DEFAULT
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_SCHEMA = 'your_database' AND TABLE_NAME = 'your_table';
```

### 2.4 **查询表的索引信息：**

```sql
SELECT * FROM INFORMATION_SCHEMA.STATISTICS
WHERE TABLE_SCHEMA = 'your_database' AND TABLE_NAME = 'your_table';
```

---

## 3. 元数据缓存

MySQL 会将部分元数据缓存起来，避免每次查询都从磁盘加载。这个缓存由 `information_schema` 和其他系统表提供，但对于大量的数据库和表，缓存的开销和管理可能会对性能产生影响。

- **查询缓存**：MySQL 默认会缓存一些查询结果，包括元数据查询，尤其是在 `INFORMATION_SCHEMA` 中的数据。可以通过查询 `SHOW STATUS LIKE 'Qcache%'` 来查看缓存状态。
- **刷新缓存**：如果更改了数据库结构（例如添加/删除表或列），你可以通过以下命令刷新缓存：

```sql
FLUSH TABLES;
```

---
