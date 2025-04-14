# Redis基础-JSON

## 1. Redis JSON

Redis 本身不支持 JSON，但通过官方模块 **RedisJSON**，你可以像操作文档数据库一样直接存储、检索和修改 JSON 数据结构。

它支持：

- 嵌套 JSON 结构（对象、数组）
- 路径访问（类似 JSONPath）
- 类型感知
- 原子更新、删除子字段
- 查询、索引（搭配 RediSearch）

> 是 Redis 官方模块，适用于需要文档结构但又不想上 MongoDB 的场景。

---

## 2. JSON 数据结构示例

```json
{
  "id": 123,
  "name": "Alice",
  "location": {
    "city": "Shenzhen",
    "geo": [114.05, 22.55]
  },
  "tags": ["dev", "golang", "redis"]
}
```

这个结构可以存入 Redis，并支持按路径访问和修改。

---

## 3. 安装 RedisJSON 模块

否则使用 Redis 模块机制加载：

```bash
redis-server --loadmodule /path/to/rejson.so
```

---

## 4. 核心命令详解

RedisJSON 命令以 `JSON.` 前缀开头：

---

### 4.1 写入 JSON 数据

```bash

JSON.SET key path value
```

示例：

```bash

JSON.SET user:1 $ '{"id":1,"name":"Alice","age":25}'
```

- `$` 表示整个 JSON 根路径。

---

### 4.2 获取 JSON 内容

```bash

JSON.GET key [path]
```

示例：

```bash

JSON.GET user:1 $
# 返回完整 JSON
```

```bash

JSON.GET user:1 $.name
# 返回: ["Alice"]
```

---

### 4.3 修改字段

```bash

JSON.SET key path new_value
```

示例：

```bash

JSON.SET user:1 $.age 26
```

也支持嵌套字段：

```bash

JSON.SET user:1 $.location.city '"Guangzhou"'
```

---

### 4.4 删除字段

```bash

JSON.DEL key [path]
```

示例：

```bash

JSON.DEL user:1 $.tags
```

---

### 4.5 判断字段是否存在

```bash

JSON.TYPE key path
```

返回值示例：

- `"string"`
- `"number"`
- `"object"`
- `"array"`
- `"null"`
- `nil`（不存在）

---

### 4.6 数组操作

```bash

JSON.ARRAPPEND key path value [value ...]     # 追加
JSON.ARRPOP    key path [index]               # 弹出
JSON.ARRLEN    key path                       # 长度
```

示例：

```bash

JSON.ARRAPPEND user:1 $.tags '"redis"' '"backend"'
JSON.ARRPOP user:1 $.tags -1     # 删除最后一个元素
JSON.ARRLEN user:1 $.tags
```

---

### 4.7 数值原子递增

```bash

JSON.NUMINCRBY key path increment
```

示例：

```bash

JSON.NUMINCRBY user:1 $.age 1
```

---

### 4.8 获取子字段名（对象的键）

```bash

JSON.OBJKEYS key path
```

示例：

```bash

JSON.OBJKEYS user:1 $.location
```

---

### 4.9 获取对象键值对数量

```bash

JSON.OBJLEN key path
```

---



