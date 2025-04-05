# Shell基础-循环

## 1.`for` 循环

### 1.1 遍历列表（字符串或数组）

```bash

for item in a b c; do
  echo "$item"
done
```

也可以是：

```bash

arr=(apple banana cherry)
for fruit in "${arr[@]}"; do
  echo "$fruit"
done
```

注意 `[@]` 保证元素内有空格时也能正确解析。

---

### 1.2 算术循环

```bash

for ((i=0; i<5; i++)); do
  echo "i=$i"
done
```

这是 Bash 独有的，适用于数字范围，性能好且语义清晰。

---

## 2. `while` 循环

适合在某个**条件为真时持续执行**：

```bash

i=0
while [ $i -lt 5 ]; do
  echo "i=$i"
  ((i++))
done
```


## 3. `until` 循环

和 `while` 相反，**直到条件为真才停止**：

```bash

i=0
until [ $i -ge 5 ]; do
  echo "i=$i"
  ((i++))
done
```

---

## 4. 循环控制语句

| 命令     | 含义                   |
|----------|------------------------|
| `break`  | 终止整个循环           |
| `continue` | 跳过本次循环，执行下一次 |

示例：

```bash
# 大括号展开（Brace Expansion）生成一组连续的字符串或数字
for i in {1..5}; do
  if [ $i -eq 3 ]; then
    continue
  fi
  echo "$i"
done
```

