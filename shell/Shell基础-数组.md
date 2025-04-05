# Shell基础-数组

Bash 中支持 **一维数组**，下标从 **0 开始**，不支持多维数组。

数组分两种：
- **普通数组**（数值下标）
- **关联数组（哈希表）**：Bash 4.0+ 才支持


## 1.普通数组操作

### 1.1 定义数组

```bash

arr=(apple banana cherry)
```

或指定下标：

```bash

arr[0]=apple
arr[1]=banana
arr[2]=cherry
```

### 1.2 读取数组元素

```bash

echo "${arr[0]}"   # 输出 apple
```

### 1.3 读取全部元素

```bash

echo "${arr[@]}"   # 推荐，用于带空格元素
echo "${arr[*]}"   # 所有元素当成一个字符串
```

> 加引号时的区别见下文。

### 1.4 获取元素个数

```bash

echo "${#arr[@]}"   # 推荐
echo "${#arr[*]}"   # 效果一样
```

### 1.5 遍历数组

```bash

for item in "${arr[@]}"; do
  echo "$item"
done
```

### 1.6 修改元素

```bash

arr[1]="blueberry"
```

### 1.7 删除元素

```bash

unset arr[1]
```

这会导致 **下标1不再存在**，但不会重排。

---

## 2. `${arr[*]}` vs `${arr[@]}` 的区别

| 表达式 | 含义 | 示例（含空格元素） |
|--------|------|----------------------|
| `"${arr[*]}"` | 所有元素合成**一个字符串** | `"a b c"` |
| `"${arr[@]}"` | 每个元素一个字符串（保留分隔） | `"a"` `"b"` `"c"` |

使用 `"${arr[@]}"`，可避免空格带来的解析错误。

---

## 3. 数组切片（子数组）

```bash

arr=(a b c d e f)
echo "${arr[@]:1:3}"   # 从索引 1 开始取 3 个元素：b c d
```

---

## 4. 关联数组（键值对）—— Bash 4.0+

```bash

# 声明一个关联数组
declare -A user
user[name]="Alice"
user[age]=20
user[city]="Beijing"

echo "${user[name]}"   # Alice
```

遍历关联数组：

```bash

for key in "${!user[@]}"; do
  echo "$key => ${user[$key]}"
done
```

---
