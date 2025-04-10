# Python基础-文件IO

## 1.文件打开与关闭

### 1.1 **打开文件** — `open()`

在 Python 中，使用 `open()` 函数打开文件，该函数返回一个文件对象，通过该对象可以进行读写操作。

```python
file = open('example.txt', mode='r')
```

- **常用模式：**
    - `'r'`：只读模式（默认模式）。文件必须存在。
    - `'w'`：写入模式。如果文件存在，会覆盖原有内容；如果文件不存在，会创建新文件。
    - `'a'`：追加模式。如果文件存在，写入数据会添加到文件末尾；如果文件不存在，会创建新文件。
    - `'b'`：二进制模式，用于处理二进制文件（如图片、音频等）。
    - `'x'`：创建并打开文件。如果文件已存在，操作会失败。
    - `'t'`：文本模式（默认模式）。

#### 示例：

```python
# 以读取模式打开文件
file = open('example.txt', 'r')

# 以写入模式打开文件
file = open('example.txt', 'w')

# 以追加模式打开文件
file = open('example.txt', 'a')
```

### 1.2 **关闭文件** — `file.close()`

每次打开文件后，都应该调用 `close()` 方法关闭文件。这有助于释放文件资源，并确保文件中的数据被正确写入磁盘。

```python
file.close()
```

### 1.3 **使用 `with` 语句**（推荐）

为了确保文件在使用完后被自动关闭，推荐使用 `with` 语句，这样即使发生异常，文件也会被自动关闭。

```python
with open('example.txt', 'r') as file:
    content = file.read()
```

这种方式是最常用的文件处理方式。

---

## 2. 文件读取操作

### 2.1 **`read()`** — 读取整个文件内容

`read()` 方法会读取文件的全部内容并返回一个字符串。

```python
with open('example.txt', 'r') as file:
    content = file.read()
    print(content)
```

### 2.2 **`read(n)`** — 读取指定字节数

`read(n)` 方法读取文件的前 `n` 个字符或字节。

```python
with open('example.txt', 'r') as file:
    content = file.read(5)  # 读取前5个字符
    print(content)
```

### 2.3 **`readline()`** — 读取一行

`readline()` 方法每次读取文件的一行，返回一个字符串。

```python
with open('example.txt', 'r') as file:
    line = file.readline()
    print(line)
```

### 2.4 **`readlines()`** — 读取所有行

`readlines()` 方法将文件的每一行作为一个元素读取到列表中。

```python
with open('example.txt', 'r') as file:
    lines = file.readlines()
    print(lines)
```

---

## 3. 文件写入操作

### 3.1 **`write()`** — 写入字符串

`write()` 方法将字符串写入文件。如果文件以 `'w'` 模式打开，则会覆盖文件内容；如果以 `'a'` 模式打开，则会在文件末尾追加。

```python
with open('example.txt', 'w') as file:
    file.write("Hello, world!")
```

### 3.2 **`writelines()`** — 写入多行

`writelines()` 方法将一个字符串序列写入文件，每个字符串会被逐一写入。

```python
lines = ["Hello, world!\n", "This is Python.\n", "Goodbye!"]

with open('example.txt', 'w') as file:
    file.writelines(lines)
```

注意，`writelines()` 不会自动添加换行符，需手动添加。

---

## 4. 二进制文件操作

Python 也支持二进制文件的读取和写入（如图片、视频等），需要在打开文件时使用 `'b'` 模式。

### 4.1 **读取二进制文件**

```python
with open('example.jpg', 'rb') as file:
    content = file.read()
    print(content)
```

### 4.2 **写入二进制文件**

```python
with open('example_copy.jpg', 'wb') as file:
    file.write(content)
```

### 4.3 **`readinto()`** — 将数据直接读取到指定缓冲区

`readinto()` 方法允许你将数据直接读取到一个已有的缓冲区中。

```python
buffer = bytearray(1024)

with open('example.jpg', 'rb') as file:
    file.readinto(buffer)

print(buffer[:50])  # 打印前50个字节
```

---

## 5. 文件光标位置

文件读取和写入时，会有一个光标位置。可以通过以下方法来控制和查询光标位置。

### 5.1 **`tell()`** — 获取当前光标位置

`tell()` 方法返回当前文件对象的光标位置。

```python
with open('example.txt', 'r') as file:
    content = file.read(5)
    print(file.tell())  # 输出当前光标位置
```

### 5.2 **`seek(offset, whence=0)`** — 移动光标

`seek()` 方法用于移动文件的光标位置。`offset` 是偏移量，`whence` 决定从哪里开始计算偏移，默认为 `0`（文件开头）。

```python
with open('example.txt', 'r') as file:
    file.seek(5)  # 将光标移动到文件的第 5 个字节
    content = file.read(5)
    print(content)
```

`whence` 参数：
- `os.SEEK_SET`：从文件的开头开始偏移。
- `os.SEEK_CUR`：从当前光标位置开始偏移。
- `os.SEEK_END`：从文件的末尾开始偏移。

---

