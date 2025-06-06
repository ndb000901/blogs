# Shell基础-运算符

## 1. 算术运算符

### 1.1 基本运算

| 运算符 | 说明     | 示例             |
|--------|----------|------------------|
| `+`    | 加法     | `a=5; b=3; echo $((a + b))` |
| `-`    | 减法     | `a=5; b=3; echo $((a - b))` |
| `*`    | 乘法     | `a=5; b=3; echo $((a * b))` |
| `/`    | 除法     | `a=5; b=3; echo $((a / b))` |
| `%`    | 取余     | `a=5; b=3; echo $((a % b))` |

### 1.2 自增自减

```bash

((a++))  # 自增
((a--))  # 自减
((a+=2)) # a = a + 2
((a-=2)) # a = a - 2
```

### 3.3 算术比较运算符

| 运算符 | 说明               | 示例          |
|--------|--------------------|---------------|
| `-eq`  | 等于               | `[ "$a" -eq "$b" ]` |
| `-ne`  | 不等于             | `[ "$a" -ne "$b" ]` |
| `-lt`  | 小于               | `[ "$a" -lt "$b" ]` |
| `-le`  | 小于等于           | `[ "$a" -le "$b" ]` |
| `-gt`  | 大于               | `[ "$a" -gt "$b" ]` |
| `-ge`  | 大于等于           | `[ "$a" -ge "$b" ]` |

---

## 2. 逻辑运算符

### 2.1 布尔运算符

| 运算符  | 说明       | 示例                                  |
|------|------------|-------------------------------------|
| `&&` | 与（AND）  | `[ -f file.txt ] && echo "文件存在"`    |
| `\|\|`   | 或（OR）   | `[ -f file.txt ] \|\| echo "文件不存在"` |
| `!`  | 非（NOT）  | `! [ -f file.txt ] && echo "文件不存在"` |

- `&&`：前一个命令成功（返回值为 0）时执行后一个命令。
- `||`：前一个命令失败（返回值非 0）时执行后一个命令。
- `!`：取反，返回条件的相反值。

### 2.2 组合条件判断

```bash

if [ -f file.txt ] && [ -r file.txt ]; then
  echo "文件存在且可读"
fi
```

---

## 3. 字符串运算符

### 3.1 字符串比较运算符

| 运算符 | 说明           | 示例                           |
|--------|----------------|--------------------------------|
| `=`    | 字符串相等     | `[ "$a" = "$b" ]`             |
| `!=`   | 字符串不等     | `[ "$a" != "$b" ]`            |
| `<`    | 字符串字典序小 | `[ "$a" \< "$b" ]`            |
| `>`    | 字符串字典序大 | `[ "$a" \> "$b" ]`            |
| `-z`   | 字符串为空     | `[ -z "$a" ]`                 |
| `-n`   | 字符串非空     | `[ -n "$a" ]`                 |

注意：字符串比较用 `=` 和 `!=`，而不是 `-eq` 或 `-ne`，因为后者用于整数比较。

### 3.2 字符串长度运算符

| 运算符 | 说明            | 示例                              |
|--------|-----------------|-----------------------------------|
| `-z`   | 字符串为空      | `if [ -z "$var" ]; then echo "为空"; fi` |
| `-n`   | 字符串非空      | `if [ -n "$var" ]; then echo "非空"; fi` |

---

## 4. 文件运算符

| 运算符 | 说明              | 示例                                |
|--------|-------------------|-------------------------------------|
| `-e`   | 文件存在          | `[ -e file.txt ]`                  |
| `-f`   | 是普通文件        | `[ -f file.txt ]`                  |
| `-d`   | 是目录            | `[ -d /path/to/dir ]`              |
| `-r`   | 可读              | `[ -r file.txt ]`                  |
| `-w`   | 可写              | `[ -w file.txt ]`                  |
| `-x`   | 可执行            | `[ -x file.txt ]`                  |
| `-s`   | 文件非空          | `[ -s file.txt ]`                  |
| `-L`   | 是符号链接        | `[ -L file.txt ]`                  |
| `-nt`  | 比较文件是否更新  | `[ file1.txt -nt file2.txt ]`      |
| `-ot`  | 比较文件是否过时  | `[ file1.txt -ot file2.txt ]`      |

---

## 5. 重定向运算符

| 运算符    | 说明                           | 示例                       |
|-----------|--------------------------------|----------------------------|
| `>`       | 将输出重定向到文件             | `echo "Hello" > file.txt`  |
| `>>`      | 追加输出到文件                 | `echo "Hello" >> file.txt` |
| `<`       | 从文件中读取输入               | `read var < file.txt`      |
| `2>`      | 将标准错误重定向到文件         | `command 2> error.log`     |
| `&>`      | 将标准输出和错误都重定向到文件 | `command &> output.log`    |

---

## 6. 赋值运算符

| 运算符 | 说明      | 示例                        |
|--------|-----------|-----------------------------|
| `=`    | 赋值      | `var=10`                    |
| `+=`   | 累加      | `var+=10`                   |
| `-=`   | 累减      | `var-=10`                   |
| `*=`   | 乘法赋值  | `var*=10`                   |
| `/=`   | 除法赋值  | `var/=10`                   |

---

