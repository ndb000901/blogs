# Python基础-OS


## 1. `os` 模块概述

`os` 模块提供了丰富的操作系统接口，可以用来进行文件、目录管理以及与操作系统的其他交互。

常见用途：
- 文件和目录操作（如创建、删除、重命名、遍历目录等）。
- 获取和设置环境变量。
- 执行操作系统命令。
- 获取和管理进程信息。
- 路径处理等。

---

## 2. 常用功能

### 2.1 **文件和目录操作**

#### `os.mkdir(path, mode=0o777)`
用于创建一个目录。如果目录已经存在，则会引发 `FileExistsError`。

```python
import os

# 创建一个目录
os.mkdir("myfolder")
```

#### `os.makedirs(name, mode=0o777, exist_ok=False)`
递归创建目录。如果父目录不存在，可以一并创建。`exist_ok=True` 时，如果目录已存在不会抛出错误。

```python
os.makedirs("parent_dir/child_dir", exist_ok=True)
```

#### `os.rmdir(path)`
用于删除一个空目录。如果目录非空，则会抛出 `OSError`。

```python
os.rmdir("myfolder")
```

#### `os.remove(path)`
删除文件。如果文件不存在，则抛出 `FileNotFoundError`。

```python
os.remove("file.txt")
```

#### `os.rename(src, dst)`
用于重命名文件或目录。

```python
os.rename("old_name.txt", "new_name.txt")
```

#### `os.listdir(path)`
返回指定路径下的所有文件和目录的列表。

```python
print(os.listdir("."))
```

#### `os.path.exists(path)`
检查路径是否存在。返回 `True` 或 `False`。

```python
if os.path.exists("file.txt"):
    print("文件存在")
```

---

### 2.2 **路径操作**

#### `os.path.join(*paths)`
将一个或多个路径组合成一个路径。适应不同操作系统的路径分隔符。

```python
path = os.path.join("folder", "subfolder", "file.txt")
print(path)  # 输出: folder/subfolder/file.txt (Linux 或 macOS)
```

#### `os.path.basename(path)`
返回指定路径的最后部分（文件名或目录名）。

```python
print(os.path.basename("/path/to/file.txt"))  # 输出: file.txt
```

#### `os.path.dirname(path)`
返回路径的目录部分。

```python
print(os.path.dirname("/path/to/file.txt"))  # 输出: /path/to
```

#### `os.path.abspath(path)`
返回路径的绝对路径。

```python
print(os.path.abspath("file.txt"))
```

#### `os.path.isabs(path)`
检查路径是否为绝对路径。

```python
print(os.path.isabs("/path/to/file.txt"))  # True
```

#### `os.path.exists(path)`
检查给定路径是否存在。

```python
print(os.path.exists("/path/to/file.txt"))  # True 或 False
```

#### `os.path.isfile(path)`
检查路径是否是文件。

```python
print(os.path.isfile("file.txt"))  # True 或 False
```

#### `os.path.isdir(path)`
检查路径是否是目录。

```python
print(os.path.isdir("myfolder"))  # True 或 False
```

---

### 2.3 **环境变量**

#### `os.getenv(key, default=None)`
获取指定环境变量的值。如果没有该变量，则返回默认值。

```python
print(os.getenv("HOME"))  # 输出主目录路径
```

#### `os.putenv(key, value)`
设置一个环境变量。

```python
os.putenv("MY_VAR", "my_value")
```

#### `os.environ`
返回一个包含所有环境变量的字典。可以通过它读取和设置环境变量。

```python
# 获取环境变量
print(os.environ["HOME"])

# 设置环境变量
os.environ["MY_VAR"] = "my_value"
```

---

### 2.4 **进程管理**

#### `os.getpid()`
返回当前进程的进程 ID。

```python
print(os.getpid())  # 输出当前进程的 ID
```

#### `os.getppid()`
返回当前进程的父进程 ID。

```python
print(os.getppid())  # 输出父进程 ID
```

#### `os.fork()`
创建一个子进程。返回值为 0 表示子进程，返回值为子进程的 PID 表示父进程。

```python
pid = os.fork()
if pid == 0:
    print("这是子进程")
else:
    print(f"这是父进程，子进程 PID 是 {pid}")
```

#### `os.execv(path, args)`
在当前进程中执行指定的程序。

```python
os.execv("/bin/ls", ["ls", "-l"])
```

#### `os.system(command)`
执行操作系统的命令，返回命令的退出状态码。

```python
os.system("echo Hello, World!")
```

---

### 2.5 **文件描述符和 I/O 操作**

#### `os.open(path, flags, mode=0o777)`
打开文件，返回文件描述符。

```python
fd = os.open("file.txt", os.O_RDWR | os.O_CREAT)
```

#### `os.read(fd, n)`
从文件描述符 `fd` 读取最多 `n` 字节。

```python
content = os.read(fd, 100)
```

#### `os.write(fd, data)`
将数据写入文件描述符 `fd`。

```python
os.write(fd, b"Hello, World!")
```

#### `os.close(fd)`
关闭文件描述符。

```python
os.close(fd)
```

---

### 2.6 **执行操作系统命令**

#### `os.system(command)`
执行系统命令，并返回命令的退出状态码。

```python
os.system("ls -l")
```

#### `os.popen(command)`
执行系统命令，并返回一个可以进行读写操作的文件对象。

```python
with os.popen("ls -l") as stream:
    output = stream.read()
    print(output)
```

---

## 3. 其他功能

### 3.1 **临时文件和目录**

#### `os.tempnam([dir[, prefix]])`
返回一个唯一的临时文件路径。

```python
temp_file = os.tempnam()
print(temp_file)  # 输出临时文件路径
```

#### `os.tmpfile()`
创建一个临时文件并以二进制读写模式打开。文件将在关闭时自动删除。

```python
with os.tmpfile() as temp_file:
    temp_file.write(b"Hello, Temp!")
```

### 3.2 **文件锁定**

`os` 模块没有直接提供文件锁定的功能，但你可以使用其他库，如 `fcntl` 来处理文件锁定。

---

