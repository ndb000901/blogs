# Shell基础-参数传递



## 1. 位置参数


| 参数     | 含义 |
|----------|------|
| `$0`     | 当前脚本名 |
| `$1`~`$9` | 传入的第1~9个参数 |
| `${10}` | 第10个参数及以后，必须加花括号 |
| `$#`     | 参数个数 |
| `$*`     | 所有参数，作为一个字符串 |
| `$@`     | 所有参数，保留边界（推荐） |
| `$?`     | 上一条命令的退出码 |
| `$$`     | 当前脚本的进程 PID |
| `$!`     | 上一个后台任务的 PID |



### 示例

```bash

#!/bin/bash
echo "脚本名：$0"
echo "第一个参数：$1"
echo "第二个参数：$2"
echo "总共 $# 个参数"
```

运行方式：

```bash

./myscript.sh hello world
```

输出：

```
脚本名：./myscript.sh
第一个参数：hello
第二个参数：world
总共 2 个参数
```

---

## 2. `$*` vs `$@` 的区别

### 相同点：

- 都是**引用所有参数**
- 在未加引号时，效果一样

### 区别（加引号时）：

```bash

for arg in "$*"; do
  echo "$arg"
done
```

`$*` 视为一个整体参数（全部拼成一个）

```bash

for arg in "$@"; do
  echo "$arg"
done
```

`$@` 是多个参数，逐个迭代

---

## 3. `shift` 命令（常用于处理参数）

- `shift` 会把 `$2` 变成 `$1`，`$3` 变 `$2`，以此类推
- 可用来写参数处理循环

### 示例：

```bash

while [ $# -gt 0 ]; do
  echo "处理参数：$1"
  shift
done
```

---

## 4. 参数扩展

这些表达可以做默认值、错误提示、字符串裁剪等

| 语法 | 作用 | 示例 |
|------|------|------|
| `${var:-default}` | 如果未定义，使用默认值 | `echo ${name:-guest}` |
| `${var:=default}` | 如果未定义，赋值默认值 | `echo ${name:=guest}` |
| `${var:+alt}` | 如果已定义，返回 alt | `echo ${name:+hello}` |
| `${var:?msg}` | 如果未定义，报错 | `echo ${name:?请设置name}` |
| `${#var}` | 变量长度 | `echo ${#name}` |
| `${var%pattern}` | 从结尾删除最短匹配 | `echo ${file%.txt}` |
| `${var%%pattern}` | 从结尾删除最长匹配 | |
| `${var#pattern}` | 从开头删除最短匹配 | |
| `${var##pattern}` | 从开头删除最长匹配 | |

