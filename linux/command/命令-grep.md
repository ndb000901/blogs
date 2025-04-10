# grep 命令


`grep` 是 Linux / Unix 系统中最常用的文本搜索工具之一，它能在文件或标准输出中查找匹配指定模式的文本行。使用灵活，支持正则表达式，可与管道配合使用，功能极其强大。

---

## 1. 基本语法

```bash

grep [选项] PATTERN [FILE...]
```

- `PATTERN`：要查找的字符串或正则表达式。
- `FILE`：要查找的目标文件，也可以从标准输入中读取。

---

## 2. 常用选项

| 选项 | 说明 |
|------|------|
| `-i` | 忽略大小写（Ignore case） |
| `-v` | 反向匹配：只显示不匹配的行 |
| `-r` / `-R` | 递归搜索子目录（包括符号链接） |
| `-n` | 显示匹配行的行号 |
| `-l` | 只列出包含匹配内容的文件名 |
| `-L` | 只列出不包含匹配内容的文件名 |
| `-c` | 显示匹配的行数 |
| `-o` | 只输出匹配到的字符串部分 |
| `-e` | 指定多个模式进行匹配 |
| `-A N` | 匹配行及其 **后** N 行（after） |
| `-B N` | 匹配行及其 **前** N 行（before） |
| `-C N` | 匹配行及其前后 N 行（context） |
| `--color` | 给匹配的内容加上颜色，便于识别 |

---

## 3. 示例

### 3.1 基本查找

```bash

grep "main" hello.c
```
查找 `hello.c` 文件中包含字符串 `main` 的行。

---

### 3.2 忽略大小写

```bash

grep -i "hello" file.txt
```
匹配 "hello"、"HELLO"、"Hello" 等，忽略大小写。

---

### 3.3 统计匹配次数

```bash

grep -c "error" /var/log/syslog
```
统计 `syslog` 文件中包含 `error` 的行数。

---

### 3.4 显示匹配的行号

```bash

grep -n "function" code.c
```
显示匹配行及其在文件中的行号。

---

### 3.5 显示匹配内容前后文

```bash

grep -C 2 "ERROR" log.txt
```
显示匹配行及其上下 2 行。

---

### 3.6 只显示匹配内容（而不是整行）

```bash

grep -o "[0-9]\+" file.txt
```
只显示文件中匹配的所有数字部分。

---

### 3.7 递归查找目录下所有 `.c` 文件中包含 `malloc`

```bash

grep -r "malloc" *.c
```

---

### 3.8 多个关键字匹配

```bash

grep -e "error" -e "failed" log.txt
```
查找包含 "error" 或 "failed" 的行。

---

### 3.9 使用管道

```bash

dmesg | grep -i usb
```
查看系统启动信息中关于 USB 的部分。

---

## 4. 注意事项

- grep 默认使用 **基本正则表达式**（BRE），如果你想使用扩展正则（ERE），请使用 `egrep` 或 `grep -E`。

  ```bash
  
  grep -E "err(or|ing)" log.txt
  ```

- 对二进制文件使用 `grep` 会显示 `Binary file matches`，你可以加 `-a` 让它按文本处理：

  ```bash
  
  grep -a "pattern" binaryfile
  ```

---

