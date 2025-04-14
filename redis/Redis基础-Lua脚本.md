# Redis基础-Lua脚本

## 1.基本概念

- Redis 从 2.6 版本开始支持内嵌的 Lua 脚本。
- 脚本通过 `EVAL` 或 `EVALSHA` 命令执行。
- **脚本执行是原子的**：Redis 会在主线程中一次性完成整个脚本的执行，中途不会有其他命令插队。

---

## 2. 基本语法

```bash

EVAL script numkeys key1 key2 ... arg1 arg2 ...
```

- `script`：Lua 脚本内容
- `numkeys`：key 的数量
- `key1`、`key2`…：传入的 Redis key（用于路由、集群兼容）
- `arg1`、`arg2`…：额外的参数（不是 key）

---

### 2.1 示例一：简单加法

```bash

EVAL "return ARGV[1] + ARGV[2]" 0 2 3
```

返回结果为 `5`

---

### 2.2 示例二：Redis 命令调用

```bash

EVAL "redis.call('SET', KEYS[1], ARGV[1])" 1 mykey myvalue
```

等价于：

```bash

SET mykey myvalue
```

---

## 3. redis.call vs redis.pcall

| 函数 | 特点 |
|------|------|
| `redis.call` | 抛出错误（脚本中断） |
| `redis.pcall` | 捕获错误（返回错误对象） |

---

## 4. Lua 中访问参数

| 类型 | Lua 中变量名 | 示例 |
|------|---------------|--------|
| Key | `KEYS[n]` | `KEYS[1]` |
| 参数 | `ARGV[n]` | `ARGV[1]` |

---

## 5. EVAL 与 EVALSHA

### 5.1 `EVAL`
- 每次执行都会发送完整脚本内容

### 5.2 `EVALSHA`
- 只发送脚本的 SHA1 摘要，更省带宽
- 要配合 `SCRIPT LOAD` 使用

```bash

# 加载脚本
SCRIPT LOAD "return redis.call('GET', KEYS[1])"
# 返回 SHA1：比如 0e3f2...

# 调用脚本
EVALSHA 0e3f2... 1 mykey
```

---

## 6. 脚本原子性

所有 Redis Lua 脚本在执行时：
- **中途不会有其他客户端插入命令**
- **保证数据一致性和事务性**

---

## 7. 注意事项

| 限制 | 说明 |
|------|------|
| 脚本不能运行太久 | Redis 是单线程，长时间脚本会阻塞其它命令（默认执行时间限制为 5 秒） |
| 脚本不能使用阻塞命令 | 比如 `BLPOP` 等 |
| 最大返回值大小 | Redis 限制返回值不能超过 512MB |
| Redis Cluster 限制 | 所有 `KEYS[i]` 必须在同一个哈希槽（slot）中，否则会报错 |

---

