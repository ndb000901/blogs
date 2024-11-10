# 文件IO

## 1.open

open 是一个用于打开文件的系统调用，位于 <fcntl.h> 中。
它提供了文件访问的主要接口，可用于以不同的模式打开文件，执行读写操作，以及控制文件的创建、追加、同步等行为。

`open` 函数的 `flag` 参数用于控制文件的打开方式，指定访问模式和一些额外的选项。常用的 `flag` 参数包括以下几种：

### 基本访问模式（必选）
这三个标志中必须选择一个，用于指定文件的访问权限：

- **`O_RDONLY`**：只读模式打开文件。
- **`O_WRONLY`**：只写模式打开文件。
- **`O_RDWR`**：读写模式打开文件。

### 可选标志
可以通过按位或 (`|`) 将以下标志与访问模式组合使用，以实现更复杂的文件操作：

- **`O_CREAT`**：如果文件不存在则创建新文件，需要指定 `mode` 参数（文件权限）。
- **`O_EXCL`**：与 `O_CREAT` 一起使用时，如果文件已存在则返回错误，防止文件被覆盖。
- **`O_TRUNC`**：以写入模式打开文件时，清空文件内容。
- **`O_APPEND`**：以追加模式打开文件，每次写入时从文件末尾开始。
- **`O_NONBLOCK`**：以非阻塞模式打开文件（通常用于设备文件）。
- **`O_SYNC`**：使每次写入操作等待数据物理写入磁盘，确保数据同步。
- **`O_DSYNC`**：同步数据更新，但不保证元数据的同步（文件时间、权限等）。
- **`O_RSYNC`**：在读操作时同步更新。
- **`O_NOFOLLOW`**：不跟随符号链接，如果文件是符号链接则返回错误。
- **`O_CLOEXEC`**：在 `fork()` 后自动关闭文件描述符，常用于防止文件描述符泄漏到子进程中。

### 使用示例
```c
#include<fcntl.h>

int main(int argc, char const *argv[])
{
    int fd1 = open("/home/hello/code/c/demo/io", O_RDONLY);
    if (fd1 == -1) {
        perror("open error");
        return 1;
    }
    int fd2 = openat(fd1, "open-demo.c", O_RDONLY);
    return 0;
}


```

## 2.openat

openat 是一个系统调用，用于在指定目录下打开文件，通常用在需要相对目录操作的场景下。
openat 能让应用程序在不改变当前工作目录的情况下，在特定的目录中创建、打开或修改文件，方便在沙盒环境或者类似场景下实现更灵活的文件管理。

```c
#include<fcntl.h>

int main(int argc, char const *argv[])
{
    int fd1 = open("/home/hello/code/c/demo/io", O_RDONLY);
    if (fd1 == -1) {
        perror("open error");
        return 1;
    }
    int fd2 = openat(fd1, "open-demo.c", O_RDONLY);
    return 0;
}

```

## 3.creat

creat 是文件操作的一个常用方法，用于创建新文件并返回文件描述符。

```c
#include<fcntl.h>
#include<unistd.h>

int main(int argc, char const *argv[])
{
    int fd1 = creat("create1.txt", 0666);
    // 等价
    int fd2 = open("create2.txt", O_CREAT | O_RDONLY | O_TRUNC, 0666);
    return 0;
}

```

## 4.close

close 是一个用于关闭文件描述符的系统调用。
它在文件操作完成后释放文件描述符，确保资源不被浪费，并使数据安全地写入磁盘。
通常每打开一个文件，就应该在操作结束后调用 close，避免文件描述符泄露。

```c
#include<fcntl.h>
#include<stdio.h>

int main(int argc, char const *argv[])
{
    int fd = open("./create.txt", O_RDONLY);
    if (fd == -1) {
        perror("open error");
        return 1;
    }
    // 操作
    close(fd);
    return 0;
}

```

## 5.lseek

`lseek` 是一个用于在打开的文件中定位文件偏移量的系统调用。
它允许在文件中随机访问数据，在读取或写入文件之前调整文件读写指针的位置。
`lseek` 常用于实现文件跳转、定位读写位置和获取文件长度等操作。

### 函数原型
```c
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence);
```

- **`fd`**：文件描述符，指定操作的文件。
- **`offset`**：偏移量，根据 `whence` 定义的基准位置进行偏移。
- **`whence`**：基准位置，可以是以下三个值之一：
  - **`SEEK_SET`**：文件开头，偏移量从文件开始位置计算。
  - **`SEEK_CUR`**：当前位置，偏移量相对于当前位置。
  - **`SEEK_END`**：文件结尾，偏移量相对于文件末尾。

### 返回值
- 成功时返回新的文件偏移量。
- 失败时返回 `-1`，并设置 `errno` 以指示错误原因。

### 常见用途
- **文件跳转**：在文件中移动读写指针位置，便于从文件的任意位置开始读写。
- **获取文件大小**：将指针移动到文件末尾（`lseek(fd, 0, SEEK_END);`），返回值即为文件大小。
- **文件扩展**：在文件尾部增加偏移（如 `lseek(fd, new_size, SEEK_SET);`）来扩展文件大小。

**示例代码**

如果指向不支持随机访问的文件描述符，如管道，FIFO，网络套接字，则lseek返回-1

```shell
# cat < etc/passwd | ./lseek-demo
```

```c
#include<fcntl.h>
#include<stdio.h>
#include<unistd.h>

int main(int argc, char const *argv[])
{
    int fd = open("./create.txt", O_RDONLY);
    // 求文件大小
    int length = lseek(fd, 0, SEEK_END);
    printf("length: %d\n", length);

    // 如果文件描述符指向管道，FIFO，网络套接字，则lseek返回-1
    if (lseek(STDIN_FILENO, 0, SEEK_CUR) == -1)
    {
        perror("lseek error\n");
        return 1;
    }
    else
    {
        printf("seek ok\n");
    }
    return 0;
}

```

**空洞文件**

空洞文件包含一些未真正占用磁盘空间的空白区域，文件系统在这类文件中会跳过空洞部分，不为它们分配实际存储块。

```c
#include<fcntl.h>
#include<stdio.h>
#include<unistd.h>


int main(int argc, char const *argv[])
{
    char data[10] = "123456789";
    int fd = open("./create2.txt", O_RDWR | O_CREAT, 0666);
    if (fd == -1) {
        perror("open error\n");
    }
    else {
        printf("fd: %d\n", fd);
    }
    // 空洞文件
    // ls -ls 或 stat create2.txt
    int offset = lseek(fd, 1000000, SEEK_END);
    int size = write(fd, data, 5);
    printf("offset: %d, size: %d\n", offset, size);
    return 0;
}

```

## 6.read

`read` 是一个系统调用，用于从文件描述符中读取数据。它通常用于从文件、管道或网络套接字中读取字节数据。

### 函数原型

```c
ssize_t read(int fd, void *buf, size_t count);
```

### 参数说明

- **`fd`**：要读取的文件描述符，通常由 `open` 函数返回。
- **`buf`**：指向一个缓冲区的指针，读取的数据将存储在此缓冲区中。
- **`count`**：要读取的字节数。

### 返回值

- 成功时，返回实际读取的字节数。
- 如果到达文件末尾，返回 0。
- 失败时，返回 -1，并设置 `errno` 来指示错误原因。

### 示例代码

```c
#include<stdio.h>
#include<fcntl.h>
#include<unistd.h>

int main(int argc, char const *argv[])
{
    int fd = open("./create.txt", O_RDONLY);
    if (fd == -1) {
        perror("open error");
    }
    char buf[20];
    int size = read(fd, buf, 10);
    printf("size: %d, buf: %s\n", size, buf);
    return 0;
}

```

## 7.write
`write` 是一个系统调用，用于向文件描述符写入数据。它通常用于将数据写入文件、管道或网络套接字。

### 函数原型

```c
ssize_t write(int fd, const void *buf, size_t count);
```

### 参数说明

- **`fd`**：要写入的文件描述符，通常由 `open` 函数返回。
- **`buf`**：指向要写入数据的缓冲区的指针。
- **`count`**：要写入的字节数。

### 返回值

- 成功时，返回实际写入的字节数。
- 如果写入操作涉及到的文件描述符是 `STDOUT` 并且输出缓冲区满，返回值可能小于 `count`。
- 失败时，返回 -1，并设置 `errno` 来指示错误原因。

### 代码示例

```c
#include<stdio.h>
#include<fcntl.h>
#include<unistd.h>

int main(int argc, char const *argv[])
{
    int fd = open("./create.txt", O_WRONLY | O_CREAT, 0666);
    if (fd == -1) {
        perror("open error");
    }
    char buf[20] = "1234567890";
    int length =lseek(fd, 0, SEEK_END);
    int size = write(fd, buf, 10);
    printf("size: %d, length: %s\n", size, buf);
    return 0;
}

```

## 8.dup, dup2

`dup` 用于复制一个文件描述符，并返回一个新的文件描述符，该新描述符指向相同的文件描述符表项。

### 函数原型

```c
int dup(int oldfd);
```

#### 参数

- **`oldfd`**：要复制的原文件描述符。

#### 返回值

- 成功时，返回新的文件描述符。
- 失败时，返回 -1，并设置 `errno`。

`dup2` 允许您将一个文件描述符复制到另一个指定的文件描述符。如果目标描述符已打开，则首先关闭它。

### 函数原型

```c
int dup2(int oldfd, int newfd);
```

#### 参数

- **`oldfd`**：要复制的原文件描述符。
- **`newfd`**：目标文件描述符。如果 `newfd` 已打开，它将被关闭。

#### 返回值

- 成功时，返回 `newfd`。
- 失败时，返回 -1，并设置 `errno`。

### 示例代码

```c
#include<stdio.h>
#include<fcntl.h>
#include<unistd.h>

int main(int argc, char const *argv[])
{
    int fd1 = dup(STDIN_FILENO);
    int fd2 = dup2(STDIN_FILENO, 4);
    printf("fd1: %d, fd2: %d\n", fd1, fd2);
    close(10);
    printf("end\n");
    return 0;
}
```

## 9.sync, fsync, fdatasync

`sync`、`fsync` 和 `fdatasync` 是文件系统调用，用于将缓冲区中的数据同步写入磁盘，确保数据的一致性和持久性。它们在功能上有所不同，适用于不同的应用场景。

### `sync`

`sync` 是一个系统调用，用于将操作系统缓存中的所有已修改文件数据写入磁盘。`sync` 不会等待写操作完成，即它只是发出写入请求，但不保证数据在 `sync` 返回时已经写入磁盘。

#### 函数原型

```c
void sync(void);
```

#### 特点
- 同步系统中的所有文件。
- 不阻塞调用者，不保证数据写入磁盘完成后才返回。

### `fsync`

`fsync` 是一个更细粒度的同步调用，它将指定文件描述符对应的文件数据同步写入磁盘，并等待写入完成。它确保文件的所有数据块和元数据都写入磁盘，从而保证文件在系统崩溃后仍然一致。

#### 函数原型

```c
int fsync(int fd);
```

#### 参数

- **`fd`**：要同步的文件描述符。

#### 返回值

- 成功时返回 `0`，失败时返回 `-1`，并设置 `errno`。

#### 特点
- 只同步指定文件。
- 会等待写入操作完成后才返回，确保数据持久化。

### `fdatasync`

`fdatasync` 与 `fsync` 类似，但它只同步文件数据内容，不同步元数据（如修改时间、访问时间）。只要文件内容一致，而不关注元数据时，`fdatasync` 比 `fsync` 更高效。

#### 函数原型

```c
int fdatasync(int fd);
```

#### 参数

- **`fd`**：要同步的文件描述符。

#### 返回值

- 成功时返回 `0`，失败时返回 `-1`，并设置 `errno`。

#### 特点
- 只同步文件数据内容，不同步元数据。
- 常用于只关心数据一致性的场景，如数据库文件同步。

### 总结

| 函数       | 同步范围                   | 是否等待完成 | 备注                                   |
|------------|----------------------------|--------------|----------------------------------------|
| `sync`     | 系统中所有文件              | 否           | 发出请求但不等待完成                   |
| `fsync`    | 指定文件的所有数据和元数据  | 是           | 等待文件数据和元数据写入完成           |
| `fdatasync`| 指定文件的数据内容（不含元数据）| 是        | 更高效，适用于数据一致性要求的场景       | 

选择使用哪种同步函数，取决于对同步范围和效率的要求。在数据持久化敏感的场景，如数据库或关键文件更新，建议使用 `fsync` 或 `fdatasync`。

### 代码示例

```
#include<unistd.h>
#include<fcntl.h>
#include<stdio.h>

int main(int argc, char const *argv[])
{
    char buf[20] = "1234567890";
    int fd = open("./create.txt", O_RDWR | O_CREAT, 0666);
    if (fd == -1)
    {
        perror("open error\n");
    }
    int size = write(fd, buf, 10);
    int result1 = fsync(fd);
    int result2 = fdatasync(fd);
    sync();
    printf("size: %d, result1: %d, result2: %d\n", size, result1, result2);
    close(fd);
    return 0;
}

```

## 10.fcntl

`fcntl` 是 Linux 和 UNIX 系统中一个强大的文件控制操作函数，允许程序对已打开的文件描述符执行各种控制操作。`fcntl` 函数不仅可以用来管理文件锁，也可以用于更改文件描述符的属性，比如设置非阻塞模式、复制文件描述符等。它的定义通常在 `<fcntl.h>` 头文件中：

```c
#include <fcntl.h>
int fcntl(int fd, int cmd, ... /* arg */ );
```

参数说明：
- **fd**：文件描述符，指明要操作的文件。
- **cmd**：控制命令，决定 `fcntl` 要执行的操作。
- **arg**：可选参数，与 `cmd` 有关，不同命令时该参数含义和使用情况不同。

`fcntl` 函数在 Linux 系统中支持多个控制命令，用于实现文件描述符的各种操作。下面详细介绍其中的 11 个常用命令及其应用。

### 1. `F_DUPFD`
复制文件描述符，找到大于或等于指定整数 `arg` 的最小文件描述符。
- **返回值**：返回新的文件描述符。
- **用途**：复制文件描述符，用于 I/O 重定向。
  
示例：
```c
int new_fd = fcntl(fd, F_DUPFD, 10); // 创建一个大于等于 10 的新文件描述符
```

### 2. `F_DUPFD_CLOEXEC`
与 `F_DUPFD` 类似，但为复制的文件描述符设置 `FD_CLOEXEC` 标志。
- **返回值**：返回新的文件描述符。
- **用途**：复制文件描述符，同时设置为执行时关闭（避免子进程继承）。

示例：
```c
int new_fd = fcntl(fd, F_DUPFD_CLOEXEC, 10);
```

### 3. `F_GETFD`
获取文件描述符的标志（只对文件描述符本身，不涉及文件状态）。
- **返回值**：返回文件描述符标志，通常用于获取 `FD_CLOEXEC`。
  
示例：
```c
int flags = fcntl(fd, F_GETFD);
```

### 4. `F_SETFD`
设置文件描述符的标志，通常设置 `FD_CLOEXEC` 标志。
- **用途**：设置标志防止子进程继承文件描述符，常用于安全性控制。

示例：
```c
fcntl(fd, F_SETFD, FD_CLOEXEC);
```

### 5. `F_GETFL`
获取文件描述符的状态标志（影响文件描述符行为）。
- **返回值**：返回文件状态标志，如 `O_APPEND` 或 `O_NONBLOCK`。

示例：
```c
int flags = fcntl(fd, F_GETFL);
```

### 6. `F_SETFL`
设置文件描述符的状态标志。常用标志有：
   - `O_APPEND`：追加模式
   - `O_NONBLOCK`：非阻塞模式

- **用途**：用于设置文件的读写模式，调整文件的操作特性。

示例：设置文件为非阻塞模式
```c
int flags = fcntl(fd, F_GETFL);
fcntl(fd, F_SETFL, flags | O_NONBLOCK);
```

### 7. `F_GETOWN`
获取文件的所有者（用于接收异步通知的进程 ID 或进程组 ID）。
- **返回值**：返回文件的所有者。
- **用途**：用于异步 I/O 或接收信号时，查询通知目标进程。

示例：
```c
int owner = fcntl(fd, F_GETOWN);
```

### 8. `F_SETOWN`
设置文件的所有者进程 ID 或进程组 ID，接收信号 `SIGIO`。
- **用途**：用于异步 I/O 通知的设置，确保特定进程接收通知信号。

示例：将文件所有者设置为当前进程
```c
fcntl(fd, F_SETOWN, getpid());
```

### 9. `F_GETLK`
检查文件的锁状态。通过 `struct flock` 指定锁定区域。
- **返回值**：如果锁被占用，返回相应的锁信息；如果未被占用，返回 F_UNLCK。
- **用途**：查询文件或文件区域的锁状态，用于并发访问控制。

示例：
```c
struct flock fl;
fl.l_type = F_WRLCK;  // 检查写锁
fl.l_whence = SEEK_SET;
fl.l_start = 0;
fl.l_len = 0;

if (fcntl(fd, F_GETLK, &fl) == -1) {
    perror("fcntl");
}
```

### 10. `F_SETLK`
为文件设置锁，分为共享锁（F_RDLCK）和独占锁（F_WRLCK）。该命令是非阻塞的，如果无法获取锁会立即返回。
- **用途**：用于进程间文件锁定，实现简单的并发控制。

示例：设置写锁（非阻塞）
```c
struct flock fl;
fl.l_type = F_WRLCK;
fl.l_whence = SEEK_SET;
fl.l_start = 0;
fl.l_len = 0;

if (fcntl(fd, F_SETLK, &fl) == -1) {
    perror("fcntl");
}
```

### 11. `F_SETLKW`
与 `F_SETLK` 类似，但会阻塞等待，直到可以获取锁。
- **用途**：常用于需要锁定资源且不希望立即返回的场景，确保资源在被使用时不被其他进程干扰。

示例：阻塞等待写锁
```c
struct flock fl;
fl.l_type = F_WRLCK;
fl.l_whence = SEEK_SET;
fl.l_start = 0;
fl.l_len = 0;

if (fcntl(fd, F_SETLKW, &fl) == -1) {
    perror("fcntl");
}
```

### 总结
- **文件描述符操作**：`F_DUPFD`、`F_DUPFD_CLOEXEC`、`F_GETFD`、`F_SETFD`
- **文件状态标志操作**：`F_GETFL`、`F_SETFL`
- **异步 I/O 控制**：`F_GETOWN`、`F_SETOWN`
- **文件锁操作**：`F_GETLK`、`F_SETLK`、`F_SETLKW`

这些命令在系统编程中非常有用，尤其是在多进程文件操作、异步 I/O 和并发控制方面。

## 11.ioctl

`ioctl`（Input/Output Control）是 Linux 和 UNIX 系统中一个非常强大的系统调用，用于对设备和文件进行控制操作。`ioctl` 不仅可以操作文件描述符，还可以对设备文件执行特定的控制操作，例如改变网络设备状态、控制终端属性、磁盘操作等。

```c
#include <sys/ioctl.h>
int ioctl(int fd, unsigned long request, ...);
```

### 参数说明
- **fd**：文件描述符，通常是通过 `open` 打开文件或设备后返回的描述符。
- **request**：控制命令，决定 `ioctl` 的操作类型。不同的设备和文件类型会支持不同的命令。
- **arg**：可选参数，通常是一个指针，指向输入或输出数据的缓冲区，或一个值。

### `ioctl` 使用场景
`ioctl` 的功能高度依赖于具体的设备和文件类型，不同类型的文件和设备有不同的 `ioctl` 控制命令。以下是一些常见的 `ioctl` 使用场景：

1. **终端控制**：设置和获取终端的各种属性，如窗口大小、模式切换等。
2. **网络设备控制**：管理网络接口的状态（启用或禁用接口，设置 MAC 地址等）。
3. **磁盘操作**：获取磁盘信息，如扇区大小、分区信息等。
4. **音频设备**：控制音量、采样率等。
5. **驱动控制**：与内核驱动通信，执行设备相关操作。

### 常见用法举例

#### 1. 控制终端设备
`ioctl` 在终端设备上应用很广泛，例如获取终端窗口大小。

```c
#include <sys/ioctl.h>
#include <unistd.h>
#include <stdio.h>
#include <termios.h>

struct winsize w;
ioctl(STDOUT_FILENO, TIOCGWINSZ, &w);
printf("Terminal rows: %d, columns: %d\n", w.ws_row, w.ws_col);
```

- `TIOCGWINSZ`：获取窗口大小，填充 `winsize` 结构体，`ws_row` 和 `ws_col` 分别为终端的行数和列数。

#### 2. 磁盘信息
可以使用 `ioctl` 获取磁盘设备的块大小、分区信息等。

```c
#include <linux/fs.h>
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

int main() {
    int fd = open("/dev/sda", O_RDONLY);
    if (fd == -1) return 1;

    unsigned long block_size;
    ioctl(fd, BLKSSZGET, &block_size);
    printf("Block size: %lu\n", block_size);

    close(fd);
    return 0;
}
```

- `BLKSSZGET`：获取块设备的块大小。

### 总结
`ioctl` 是 Linux 中非常灵活和复杂的系统调用，能够对文件和设备进行精细控制，尤其适合设备驱动程序的开发和底层硬件控制。

## 12. `/dev/fd/n`

使用open函数打开/dev/fd/1, 等效于复制指定文件描述符(dup(n))

### 代码示例

```c
#include<fcntl.h>
#include<stdio.h>
#include<unistd.h>

int main(int argc, char const *argv[])
{
    int fd1 = open("/dev/fd/1", O_RDONLY);
    // 等效于复制指定描述符
    int fd2 = dup(1);
    printf("fd1: %d, fd2: %d\n", fd1, fd2);
    close(fd1);
    close(fd2);
    return 0;
}

```
