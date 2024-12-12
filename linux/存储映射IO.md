# 存储映射IO

---

### **1. mmap**
将文件或设备映射到进程的虚拟地址空间。

#### 原型：
```c
void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```

#### 参数说明：
- **`addr`**: 指定映射的起始虚拟地址（通常为 `NULL`，由内核选择合适的地址）。
- **`length`**: 映射的字节大小。
- **`prot`**: 指定映射区域的权限：
  - `PROT_READ`：可读
  - `PROT_WRITE`：可写
  - `PROT_EXEC`：可执行
  - `PROT_NONE`：无权限
- **`flags`**: 映射的类型和行为：
  - `MAP_SHARED`：共享映射（对内存的修改会反映到文件）。
  - `MAP_PRIVATE`：私有映射（修改不影响文件，采用写时复制）。
  - `MAP_ANONYMOUS`：匿名映射（无文件关联，`fd` 应为 `-1`）。
- **`fd`**: 文件描述符（对于匿名映射，设置为 `-1`）。
- **`offset`**: 文件映射的起始偏移量（必须是页大小的倍数）。

#### 返回值：
- 成功：返回映射区域的起始地址。
- 失败：返回 `MAP_FAILED`，可通过 `errno` 获取错误原因。

#### 示例：
```c
#include <fcntl.h>
#include <sys/mman.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("testfile", O_RDWR);
    if (fd < 0) {
        perror("Failed to open file");
        return -1;
    }

    size_t length = 4096; // 映射一页
    void *addr = mmap(NULL, length, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if (addr == MAP_FAILED) {
        perror("mmap failed");
        close(fd);
        return -1;
    }

    // 修改内存中的文件内容
    sprintf((char *)addr, "Hello, mmap!");

    munmap(addr, length);
    close(fd);
    return 0;
}
```

---

### **2. mprotect**
修改已映射内存区域的访问权限。

#### 原型：
```c
int mprotect(void *addr, size_t len, int prot);
```

#### 参数说明：
- **`addr`**: 映射区域的起始地址（必须页对齐）。
- **`len`**: 修改权限的区域大小。
- **`prot`**: 新的权限（与 `mmap` 中的 `prot` 参数一致）。

#### 返回值：
- 成功：返回 `0`。
- 失败：返回 `-1`。

#### 示例：
```c
#include <sys/mman.h>
#include <stdio.h>

int main() {
    void *addr = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_ANONYMOUS | MAP_PRIVATE, -1, 0);
    if (addr == MAP_FAILED) {
        perror("mmap failed");
        return -1;
    }

    // 设置为只读
    if (mprotect(addr, 4096, PROT_READ) == -1) {
        perror("mprotect failed");
    }

    munmap(addr, 4096);
    return 0;
}
```

---

### **3. msync**
将映射区域的更改同步到底层文件或存储设备。

#### 原型：
```c
int msync(void *addr, size_t length, int flags);
```

#### 参数说明：
- **`addr`**: 起始地址（页对齐）。
- **`length`**: 同步的区域大小。
- **`flags`**:
  - `MS_SYNC`：同步写入并阻塞，直到完成。
  - `MS_ASYNC`：异步写入。
  - `MS_INVALIDATE`：清除其他进程的缓存，保证一致性。

#### 返回值：
- 成功：返回 `0`。
- 失败：返回 `-1`。

#### 示例：
```c
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("testfile", O_RDWR);
    if (fd < 0) {
        perror("Failed to open file");
        return -1;
    }

    void *addr = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if (addr == MAP_FAILED) {
        perror("mmap failed");
        close(fd);
        return -1;
    }

    // 修改映射内存内容
    sprintf((char *)addr, "Hello, msync!");

    // 同步到文件
    if (msync(addr, 4096, MS_SYNC) == -1) {
        perror("msync failed");
    }

    munmap(addr, 4096);
    close(fd);
    return 0;
}
```

---

### **4. munmap**
解除内存映射。

#### 原型：
```c
int munmap(void *addr, size_t length);
```

#### 参数说明：
- **`addr`**: 映射区域的起始地址。
- **`length`**: 解除映射的区域大小（与 `mmap` 的 `length` 保持一致）。

#### 返回值：
- 成功：返回 `0`。
- 失败：返回 `-1`。

#### 示例：
```c
#include <sys/mman.h>
#include <stdio.h>

int main() {
    void *addr = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_ANONYMOUS | MAP_PRIVATE, -1, 0);
    if (addr == MAP_FAILED) {
        perror("mmap failed");
        return -1;
    }

    // 使用完成后解除映射
    if (munmap(addr, 4096) == -1) {
        perror("munmap failed");
    }

    return 0;
}
```

---

### 注意事项
1. **地址对齐**：`mmap`、`mprotect`、`msync`、`munmap` 的地址通常需要是页对齐的。
2. **资源管理**：确保正确解除映射（调用 `munmap`），否则可能造成资源泄漏。
3. **权限控制**：调整权限时需注意安全性，避免非法访问导致的崩溃或数据破坏。
4. **性能考虑**：`msync` 是昂贵操作，应避免频繁调用。
