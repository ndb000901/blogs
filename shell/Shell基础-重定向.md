# Shell基础-重定向


Shell 中的输入输出重定向是非常强大和灵活的，它允许我们将命令的标准输入、标准输出和标准错误进行重定向，以便更好地控制程序的输入和输出。

## 常用命令

| **命令**               | **说明**                                              | **示例**                                                                 |
|------------------------|-------------------------------------------------------|------------------------------------------------------------------------|
| `command > file`        | 将标准输出重定向到文件 `file`，覆盖文件内容。         | `echo "Hello, World!" > output.txt`                                      |
| `command < file`        | 将文件 `file` 的内容作为命令的标准输入。               | `sort < names.txt`                                                      |
| `command >> file`       | 将标准输出追加到文件 `file`。                          | `echo "New line" >> output.txt`                                          |
| `n > file`              | 将文件描述符 `n` 的输出重定向到文件 `file`。           | `echo "Error message" 2> error.log`                                     |
| `n >> file`             | 将文件描述符 `n` 的输出追加到文件 `file`。             | `echo "Another error message" 2>> error.log`                            |
| `n >& m`                | 将文件描述符 `n` 的输出重定向到文件描述符 `m`。       | `command 2>&1` (合并标准错误和标准输出)                                  |
| `n <& m`                | 将文件描述符 `m` 的输入重定向到文件描述符 `n`。       | `command 0<&1` (将标准输入重定向到标准输出)                             |
| `<< tag`                | 将开始标记 `tag` 和结束标记 `tag` 之间的内容作为输入。| `cat << EOF`<br> `This is a test.`<br> `EOF`                            |

---

### 1. `command > file`
#### 说明：
将命令的标准输出（stdout）重定向到指定的文件 `file` 中。

#### 用法：
```bash

command > file
```

- 如果 `file` 已经存在，它会被覆盖。
- 如果 `file` 不存在，Shell 会创建该文件并将输出写入文件。

#### 示例：
```bash
echo "Hello, World!" > output.txt
```
这个命令将字符串 "Hello, World!" 写入到 `output.txt` 文件中。如果文件已存在，它会被覆盖。

---

### 2. `command < file`
#### 说明：
将文件 `file` 的内容作为命令的标准输入（stdin）传递。

#### 用法：
```bash

command < file
```

- 该命令会从文件 `file` 中读取内容，并将其作为输入传递给 `command`。

#### 示例：
```bash

sort < names.txt
```
这个命令会将 `names.txt` 文件的内容作为输入传递给 `sort` 命令，结果是按照字母顺序排序文件中的内容。

---

### 3. `command >> file`
#### 说明：
将命令的标准输出追加到指定的文件 `file` 中，而不是覆盖原文件。

#### 用法：
```bash

command >> file
```

- 如果文件 `file` 已经存在，命令的输出会被追加到文件的末尾。
- 如果文件 `file` 不存在，Shell 会创建该文件并写入输出。

#### 示例：
```bash

echo "New line" >> output.txt
```
这个命令将 "New line" 追加到 `output.txt` 文件的末尾。

---

### 4. `n > file`
#### 说明：
将文件描述符 `n` 的输出重定向到文件 `file` 中。

#### 用法：
```bash

n > file
```

- `n` 是文件描述符，通常 0 表示标准输入（stdin），1 表示标准输出（stdout），2 表示标准错误输出（stderr）。
- 这个命令将文件描述符 `n` 的输出重定向到 `file` 中。

#### 示例：
```bash

echo "Error message" 2> error.log
```
这个命令将标准错误输出（文件描述符 2）的内容写入到 `error.log` 文件中。

---

### 5. `n >> file`
#### 说明：
将文件描述符 `n` 的输出追加到指定的文件 `file` 中。

#### 用法：
```bash

n >> file
```

- 和 `n > file` 类似，不同的是，这个命令会将文件描述符 `n` 的输出追加到文件的末尾，而不是覆盖文件的内容。

#### 示例：
```bash

echo "Another error message" 2>> error.log
```
这个命令会将标准错误输出追加到 `error.log` 文件中，而不是覆盖其内容。

---

### 6. `n >& m`
#### 说明：
将文件描述符 `n` 的输出重定向到文件描述符 `m`，通常用于合并输出。

#### 用法：
```bash

n >& m
```

- 这个命令的作用是将文件描述符 `n` 的输出重定向到文件描述符 `m`。通常用于将标准错误输出（2）与标准输出（1）合并。

#### 示例：
```bash

command 2>&1
```
这个命令将标准错误（stderr）和标准输出（stdout）合并到一起。无论是错误信息还是正常输出，都会被发送到相同的地方（通常是屏幕）。

---

### 7. `n <& m`
#### 说明：
将文件描述符 `m` 的输入重定向到文件描述符 `n`，通常用于合并输入。

#### 用法：
```bash

n <& m
```

- 这个命令会将文件描述符 `m` 的输入重定向到文件描述符 `n`。

#### 示例：
```bash

command 0<&1
```
这个命令将标准输入（0）重定向到标准输出（1），相当于从标准输出获取输入。

---

### 8. `<< tag`
#### 说明：
将开始标记 `tag` 和结束标记 `tag` 之间的内容作为命令的输入。这被称为“Here Document”或“Here-Doc”，它允许在脚本中直接嵌入多行文本。

#### 用法：
```bash

command << tag
```

- `tag` 是一个标识符，Shell 会将 `<< tag` 后面的一段内容作为标准输入提供给 `command`。标记 `tag` 后面的内容将被传递给命令，直到再次遇到标记 `tag` 为止。

#### 示例：
```bash

cat << EOF
This is a test.
Multiple lines can be entered here.
EOF
```
这个命令会将 `<< EOF` 和 `EOF` 之间的内容作为输入提供给 `cat` 命令，因此会将这些内容输出到屏幕上。


