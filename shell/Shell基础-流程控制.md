# Shell基础-流程控制

## 1.`if` 条件判断

### 基本结构：

```bash

if 条件; then
  命令
elif 条件; then
  命令
else
  命令
fi
```

### 示例：

```bash

if [ "$age" -ge 18 ]; then
  echo "成年人"
else
  echo "未成年人"
fi
```

> `[]` 内是条件表达式。注意空格！

---

### 常见条件判断表达式：

| 表达式 | 含义 |
|--------|------|
| `-z "$str"` | 字符串是否为空 |
| `-n "$str"` | 字符串是否非空 |
| `"$a" = "$b"` | 字符串是否相等 |
| `"$a" != "$b"` | 字符串是否不等 |
| `-e file` | 文件是否存在 |
| `-d file` | 是否为目录 |
| `-f file` | 是否为普通文件 |
| `-x file` | 是否可执行 |
| `-s file` | 是否非空文件 |
| `-r file` | 是否可读 |

数值比较用：

```bash

[ "$a" -gt "$b" ]    # >
[ "$a" -lt "$b" ]    # <
[ "$a" -eq "$b" ]    # ==
[ "$a" -ne "$b" ]    # !=
```

---

## 2. `case` 分支语句（类似 switch）

```bash
case "$变量" in
  模式1)
    命令 ;;
  模式2)
    命令 ;;
  *)
    默认 ;;
esac
```

### 示例：

```bash

read -p "请输入字母：" char
case $char in
  [a-z]) echo "小写字母" ;;
  [A-Z]) echo "大写字母" ;;
  *) echo "其他字符" ;;
esac
```

---

## 3. `for` / `while` / `until` 循环

### `for` 示例：

```bash

for i in {1..3}; do
  echo "第 $i 次"
done
```

### `while` 示例：

```bash

count=1
while [ $count -le 3 ]; do
  echo "count = $count"
  ((count++))
done
```

### `until` 示例：

```bash

count=1
until [ $count -gt 3 ]; do
  echo "count = $count"
  ((count++))
done
```

---

## 4. 循环控制

| 命令 | 作用 |
|------|------|
| `break` | 立即跳出整个循环 |
| `continue` | 跳过当前循环，进入下一轮 |

```bash

for i in {1..5}; do
  if [ $i -eq 3 ]; then
    continue
  fi
  echo "i = $i"
done
```

---


