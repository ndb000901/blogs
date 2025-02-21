# 异步IO

Linux 异步 I/O (Asynchronous I/O, AIO) 是一种允许应用程序在发起 I/O 请求后继续执行其他操作的机制，避免因 I/O 阻塞而影响程序的并发性和性能。

---

## **1. aio_read**
异步地从文件中读取数据。

- **原型**：
  ```c
  int aio_read(struct aiocb *aiocbp);
  ```
- **参数**：
  - `aiocbp`：指向 `struct aiocb` 的指针，描述异步 I/O 请求。
    - 重要字段：
      - `aio_fildes`：文件描述符。
      - `aio_buf`：缓冲区地址。
      - `aio_nbytes`：读取的字节数。
      - `aio_offset`：文件中的偏移量。
- **返回值**：
  - 成功返回 0。
  - 失败返回 -1 并设置 `errno`。

---

## **2. aio_write**
异步地将数据写入文件。

- **原型**：
  ```c
  int aio_write(struct aiocb *aiocbp);
  ```
- **参数**：
  - `aiocbp`：指向 `struct aiocb` 的指针，描述异步 I/O 请求。
    - 重要字段与 `aio_read` 类似。
- **返回值**：
  - 成功返回 0。
  - 失败返回 -1 并设置 `errno`。

---

## **3. aio_fsync**
将指定文件的数据和元数据同步到存储设备。

- **原型**：
  ```c
  int aio_fsync(int op, struct aiocb *aiocbp);
  ```
- **参数**：
  - `op`：操作类型。
    - `O_SYNC`：同步数据和元数据。
    - `O_DSYNC`：仅同步数据。
  - `aiocbp`：指向 `struct aiocb` 的指针，表示文件。
- **返回值**：
  - 成功返回 0。
  - 失败返回 -1 并设置 `errno`。

---

## **4. aio_error**
检查异步 I/O 请求的状态。

- **原型**：
  ```c
  int aio_error(const struct aiocb *aiocbp);
  ```
- **参数**：
  - `aiocbp`：指向 `struct aiocb` 的指针。
- **返回值**：
  - 如果请求未完成，返回 `EINPROGRESS`。
  - 如果完成，返回 0。
  - 如果出错，返回错误码。

---

## **5. aio_return**
获取完成的异步 I/O 请求的结果。

- **原型**：
  ```c
  ssize_t aio_return(struct aiocb *aiocbp);
  ```
- **参数**：
  - `aiocbp`：指向 `struct aiocb` 的指针。
- **返回值**：
  - 成功返回操作影响的字节数。
  - 失败返回 -1 并设置 `errno`。

---

## **6. aio_suspend**
等待一个或多个异步 I/O 请求完成。

- **原型**：
  ```c
  int aio_suspend(const struct aiocb *const list[], int nent, const struct timespec *timeout);
  ```
- **参数**：
  - `list`：一个指向 `aiocb` 指针的数组，表示要等待的请求。
  - `nent`：数组中的元素数量。
  - `timeout`：超时时间，`NULL` 表示无限等待。
- **返回值**：
  - 成功返回 0。
  - 超时返回 `-1` 且 `errno` 为 `EAGAIN`。
  - 其他错误返回 -1 并设置 `errno`。

---

## **7. aio_cancel**
取消未完成的异步 I/O 请求。

- **原型**：
  ```c
  int aio_cancel(int fildes, struct aiocb *aiocbp);
  ```
- **参数**：
  - `fildes`：文件描述符。
  - `aiocbp`：指向 `struct aiocb` 的指针，若为 `NULL` 表示取消与 `fildes` 相关的所有操作。
- **返回值**：
  - `AIO_CANCELED`：请求成功取消。
  - `AIO_NOTCANCELED`：有请求无法取消。
  - `AIO_ALLDONE`：所有请求已完成。
  - 失败返回 -1 并设置 `errno`。

---

## **8. lio_listio**
批量提交多个异步 I/O 请求。

- **原型**：
  ```c
  int lio_listio(int mode, struct aiocb *const list[], int nent, struct sigevent *sig);
  ```
- **参数**：
  - `mode`：
    - `LIO_WAIT`：同步等待所有请求完成。
    - `LIO_NOWAIT`：异步提交。
  - `list`：指向 `aiocb` 指针的数组，表示请求列表。
  - `nent`：数组中的请求数量。
  - `sig`：完成时通知方式（信号或回调）。
- **返回值**：
  - 成功返回 0。
  - 失败返回 -1 并设置 `errno`。

---

## **使用示例**
以下展示如何结合这些接口完成简单的异步读写：

```c
#include <aio.h>
#include <fcntl.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <errno.h>
#include <time.h>

#define FILE_PATH "test.txt"

int main() {
    int fd = open(FILE_PATH, O_RDWR | O_CREAT, 0644);
    if (fd == -1) {
        perror("open");
        return 1;
    }

    // 定义 aio 控制块
    struct aiocb cb;
    memset(&cb, 0, sizeof(cb));
    char buffer[128] = "Hello, Async I/O!";
    cb.aio_fildes = fd;
    cb.aio_buf = buffer;
    cb.aio_nbytes = strlen(buffer);
    cb.aio_offset = 0;

    // 异步写入
    if (aio_write(&cb) == -1) {
        perror("aio_write");
        close(fd);
        return 1;
    }

    // 等待写入完成
    const struct aiocb *cblist[1] = {&cb};
    aio_suspend(cblist, 1, NULL);
    if (aio_error(&cb) == 0) {
        printf("Write completed: %zd bytes\n", aio_return(&cb));
    } else {
        perror("aio_error");
    }

    // 异步读取
    memset(&cb, 0, sizeof(cb));
    char read_buf[128] = {0};
    cb.aio_fildes = fd;
    cb.aio_buf = read_buf;
    cb.aio_nbytes = sizeof(read_buf) - 1;
    cb.aio_offset = 0;

    if (aio_read(&cb) == -1) {
        perror("aio_read");
        close(fd);
        return 1;
    }

    // 等待读取完成
    aio_suspend(cblist, 1, NULL);
    if (aio_error(&cb) == 0) {
        printf("Read completed: %zd bytes: %s\n", aio_return(&cb), read_buf);
    } else {
        perror("aio_error");
    }

    close(fd);
    return 0;
}
```

---
