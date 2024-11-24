# 标准IO库

## 1.流与FILE对象

`FILE` 是 C 标准库中定义的一个结构体，具体实现因平台和标准库（如 GNU glibc、musl）而异。以下是以 GNU glibc 为例，详细解析其 `FILE` 结构体的字段。

---

### **`FILE` 的定义** 

在 GNU glibc 中，`FILE` 的实际实现（位于 `<bits/libio.h>` 中）如下所示：

```c
struct _IO_FILE {
    int _flags;                // 文件流的标志位，描述流的状态和属性
    char* _IO_read_ptr;        // 缓冲区中当前读取的位置
    char* _IO_read_end;        // 缓冲区中读取操作的结束位置
    char* _IO_read_base;       // 缓冲区中读取操作的起始位置
    char* _IO_write_base;      // 缓冲区中写入操作的起始位置
    char* _IO_write_ptr;       // 缓冲区中当前写入的位置
    char* _IO_write_end;       // 缓冲区中写入操作的结束位置
    char* _IO_buf_base;        // 缓冲区的起始地址
    char* _IO_buf_end;         // 缓冲区的结束地址
    char* _IO_save_base;       // 保存缓冲区的起始位置（备用）
    char* _IO_backup_base;     // 保存备用缓冲区的起始地址
    char* _IO_save_end;        // 保存缓冲区的结束位置
    struct _IO_marker* _markers; // 指向流标记链表，用于支持 `ungetc` 和回退
    struct _IO_FILE* _chain;   // 指向下一个文件流的指针，用于流链表
    int _fileno;               // 文件描述符，与操作系统文件描述符关联
    int _flags2;               // 扩展标志位，用于额外的状态信息
    __off_t _old_offset;       // 上次的偏移量，用于优化定位操作
    unsigned short _cur_column; // 当前输出流的列位置（用于行缓冲）
    signed char _vtable_offset; // 虚函数表偏移量
    char _shortbuf[1];         // 用于无缓冲模式的小型缓冲区
    void* _lock;               // 用于流的线程安全锁
    __off64_t _offset;         // 当前文件流的偏移量
    void* __pad1;              // 保留字段，用于扩展
    void* __pad2;              // 保留字段，用于扩展
    void* __pad3;              // 保留字段，用于扩展
    void* __pad4;              // 保留字段，用于扩展
    size_t __pad5;             // 保留字段，用于扩展
    int _mode;                 // 模式信息（主要用于宽字符流）
    char _unused2[15 * sizeof(int) - 4 * sizeof(void*) - sizeof(size_t)];
};
```

---

### **字段详解**

以下是重要字段的功能和作用：

#### **基础 I/O 操作相关**
1. **`_flags`**  
   - 描述流的属性（如读/写模式）和状态（如错误标志）。
   - 例如：
     - `_IO_NO_WRITES`：流不可写。
     - `_IO_ERR_SEEN`：流出错。
     - `_IO_EOF_SEEN`：到达文件末尾。

2. **`_fileno`**  
   - 文件描述符，与操作系统文件描述符关联。
   - 用于通过系统调用（如 `read`、`write`）与底层设备通信。

3. **`_offset`**  
   - 当前流的文件偏移量。
   - 用于支持随机访问（如 `fseek` 和 `ftell`）。

---

#### **缓冲区管理**
4. **`_IO_read_ptr`、`_IO_read_end`、`_IO_read_base`**  
   - 用于读取操作：
     - `_IO_read_base`：缓冲区中读取数据的起始地址。
     - `_IO_read_end`：缓冲区中读取数据的结束地址。
     - `_IO_read_ptr`：缓冲区中当前读取位置的指针。

5. **`_IO_write_base`、`_IO_write_ptr`、`_IO_write_end`**  
   - 用于写入操作：
     - `_IO_write_base`：缓冲区中写入数据的起始地址。
     - `_IO_write_end`：缓冲区中写入数据的结束地址。
     - `_IO_write_ptr`：缓冲区中当前写入位置的指针。

6. **`_IO_buf_base`、`_IO_buf_end`**  
   - 缓冲区的整体管理：
     - `_IO_buf_base`：缓冲区的起始地址。
     - `_IO_buf_end`：缓冲区的结束地址。

7. **`_IO_save_base`、`_IO_save_end`**  
   - 保存当前缓冲区的状态，支持操作回退（如 `ungetc`）。

---

#### **流链表与标记**
8. **`_markers`**  
   - 用于标记流中的特定位置，支持回退操作。
   - 通常与 `ungetc` 配合使用。

9. **`_chain`**  
   - 指向下一个流的指针，允许多个流形成链表结构。

---

#### **线程安全**
10. **`_lock`**  
    - 用于实现流的线程安全。
    - 在多线程环境中，标准 I/O 库使用此字段加锁。

---

#### **辅助字段**
11. **`_flags2`**  
    - 用于存储扩展标志。
    - 例如，支持宽字符流的特殊标志。

12. **`_shortbuf`**  
    - 用于无缓冲模式的小型缓冲区。

13. **`_unused2`**  
    - 保留字段，主要用于未来扩展。

---

### **`FILE` 的工作流程**

1. **打开文件时**
   - `fopen` 初始化 `FILE` 对象的字段（如文件描述符、缓冲区）。
   - 分配缓冲区，并设置缓冲模式（全缓冲、行缓冲或无缓冲）。

2. **读写文件时**
   - 数据读写先在缓冲区中操作。
   - 缓冲区满或显式调用 `fflush` 时，实际调用系统 I/O 操作。

3. **关闭文件时**
   - 调用 `fclose` 刷新缓冲区内容。
   - 释放 `FILE` 对象和缓冲区。

---

### **代码示例：查看 `FILE` 对象内容**

你可以通过调试器查看 `FILE` 对象的内部字段：

**示例代码：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("example.txt", "w");
    if (fp == NULL) {
        perror("Failed to open file");
        return 1;
    }

    // 写入数据
    fprintf(fp, "Hello, FILE structure!\n");

    // 在调试器中观察 `fp` 的字段
    fclose(fp);
    return 0;
}
```

## 2.缓冲

### **缓冲机制**

缓冲机制是 C 标准 I/O 库为提高输入/输出效率而引入的一种机制。通过在内存中使用缓冲区，标准库减少了实际的 I/O 操作次数，从而提高了性能。

---

### **缓冲的分类**

根据刷新缓冲区的策略，缓冲可以分为以下三种：

1. **全缓冲（Fully Buffered）**  
   - 数据写入缓冲区后，只有在缓冲区满时才会真正写入底层设备（如文件）。
   - **适用场景**：大多数文件操作。  
   - **默认行为**：对文件流（如 `fopen` 打开的流）。

   **示例：**
   ```c
   FILE *fp = fopen("output.txt", "w");
   fprintf(fp, "Hello, World!"); // 写入缓冲区，未立即写入文件
   fclose(fp);                   // 关闭时刷新缓冲区，写入文件
   ```

---

2. **行缓冲（Line Buffered）**  
   - 缓冲区在遇到换行符 `\n` 时自动刷新，也可以通过 `fflush` 主动刷新。
   - **适用场景**：交互式设备（如终端）。
   - **默认行为**：标准输入流（`stdin`）和标准输出流（`stdout`），前提是与交互设备（如终端）相关联。

   **示例：**
   ```c
   printf("Enter your name: "); // 缓冲区未刷新，等待用户输入
   fflush(stdout);              // 主动刷新缓冲区，输出到屏幕
   ```

---

3. **无缓冲（Unbuffered）**  
   - 数据直接写入底层设备，不使用缓冲区。
   - **适用场景**：标准错误流（`stderr`）。
   - **默认行为**：`stderr`。

   **示例：**
   ```c
   fprintf(stderr, "Error occurred!\n"); // 立即输出到屏幕
   ```

---

### **缓冲区的控制**

#### **默认缓冲行为**

1. 文件流（如 `fopen`）：全缓冲。
2. 标准输入/输出（`stdin`/`stdout`）：行缓冲（如果关联终端）。
3. 标准错误（`stderr`）：无缓冲。

#### **手动设置缓冲行为**

可以使用 `setvbuf` 或 `setbuf` 修改缓冲模式：

1. **`setbuf(FILE *stream, char *buf)`**  
   - 设置缓冲区或禁用缓冲。  
   - 如果 `buf` 为 `NULL`，流为无缓冲模式。

   **示例：**
   ```c
   FILE *fp = fopen("example.txt", "w");
   setbuf(fp, NULL); // 设置无缓冲模式
   ```

2. **`setvbuf(FILE *stream, char *buf, int mode, size_t size)`**  
   - 设置缓冲区模式。
   - 参数：
     - `buf`：缓冲区地址。
     - `mode`：缓冲模式，可选：
       - `_IOFBF`：全缓冲。
       - `_IOLBF`：行缓冲。
       - `_IONBF`：无缓冲。
     - `size`：缓冲区大小。

   **示例：**
   ```c
   FILE *fp = fopen("example.txt", "w");
   char buffer[1024];
   setvbuf(fp, buffer, _IOFBF, sizeof(buffer)); // 设置全缓冲，缓冲区大小 1024 字节
   ```

---

### **缓冲区刷新**

缓冲区刷新将缓冲数据写入底层设备或清空缓冲区内容。以下方法可用于刷新缓冲区：

1. **`fflush(FILE *stream)`**
   - 刷新指定流的缓冲区。
   - `stream` 为 `NULL` 时刷新所有输出流。

   **示例：**
   ```c
   printf("Flushing buffer...");
   fflush(stdout); // 立即刷新缓冲区，输出到屏幕
   ```

2. **关闭流时自动刷新**
   - 调用 `fclose` 时，标准库会自动刷新缓冲区。

---

### **缓冲与性能**

缓冲显著提高了 I/O 性能，减少了与底层设备交互的频率。下面是一些优化缓冲的建议：

1. **合理调整缓冲区大小**
   - 通过 `setvbuf` 增加缓冲区大小，减少实际 I/O 次数。

2. **避免频繁刷新**
   - 在非必要情况下避免频繁调用 `fflush`，以减少性能开销。

3. **正确选择缓冲模式**
   - 对交互式流使用行缓冲；对非交互式流（如文件）使用全缓冲。


## 3.二进制IO

**二进制 I/O** 是文件操作中直接读写原始数据（字节流）的方式，与文本 I/O 相比，二进制 I/O 不进行任何字符编码转换或格式化处理。这种方式特别适用于处理结构化数据（如图像、音频、二进制文件）或需要高效存储的场景。

---

### **二进制 I/O 的基本操作函数**

#### **1. 打开文件**
通过 `fopen` 使用 `"b"` 模式打开文件：
- `"rb"`：以二进制只读模式打开文件。
- `"wb"`：以二进制写模式打开文件。
- `"ab"`：以二进制追加模式打开文件。
- `"r+b"` 或 `"rb+"`：以二进制读写模式打开文件。

#### **2. 读写函数**
二进制 I/O 使用的函数包括：
- **`fread`**：从文件中读取原始数据到内存。
- **`fwrite`**：将内存中的原始数据写入文件。

**函数原型：**
```c
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);
size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);
```
- **`ptr`**：内存缓冲区地址。
- **`size`**：每个数据项的大小（单位：字节）。
- **`nmemb`**：要读写的数据项个数。
- **`stream`**：文件指针。

返回值是成功读/写的项数。

#### **3. 关闭文件**
使用 `fclose` 释放文件指针并刷新写入的数据。

---

### **二进制 I/O 示例**

#### **写入二进制数据**
将一组整数写入二进制文件：
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("data.bin", "wb"); // 以二进制写模式打开文件
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    int numbers[] = {10, 20, 30, 40, 50};
    size_t count = fwrite(numbers, sizeof(int), 5, fp); // 写入 5 个整数
    if (count != 5) {
        perror("Error writing to file");
    }

    fclose(fp);
    return 0;
}
```

#### **读取二进制数据**
读取上例中写入的整数：
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("data.bin", "rb"); // 以二进制读模式打开文件
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    int numbers[5];
    size_t count = fread(numbers, sizeof(int), 5, fp); // 读取 5 个整数
    if (count != 5) {
        perror("Error reading from file");
    }

    for (int i = 0; i < 5; i++) {
        printf("%d\n", numbers[i]);
    }

    fclose(fp);
    return 0;
}
```

---

### **二进制 I/O 的优势**

1. **高效性**：
   - 数据直接以二进制形式存储和读取，避免了文本模式中的字符编码转换。
   - 适合处理大数据量或结构化数据。

2. **数据完整性**：
   - 不会因不同平台的换行符（如 `\n` 转 `\r\n`）或文本编码（如 ASCII 与 UTF-8）的差异导致数据错误。

3. **适合特定场景**：
   - 处理图像（如 BMP、PNG）、音频（如 WAV）、压缩文件等格式。

---

### **注意事项**

1. **平台相关性**：
   - 二进制数据在不同系统上可能表现不同，如整数大小和字节序（大端或小端）。

   **解决方法**：
   - 在存储之前进行字节序转换（使用 `htonl`、`ntohl` 等函数）。
   - 写入文件时明确指定数据类型大小。

2. **数据结构的一致性**：
   - 在读写结构体时，确保内存对齐方式一致。
   - 可以使用 `#pragma pack` 或手动处理内存对齐。

   **示例：**
   ```c
   #pragma pack(1) // 强制无内存对齐
   struct Data {
       int id;
       char name[20];
       float score;
   };
   #pragma pack() // 恢复默认对齐方式
   ```

3. **缓冲区管理**：
   - 确保 `fread` 和 `fwrite` 的缓冲区足够大，以免发生越界。
   - 使用 `fflush` 强制刷新缓冲区，避免数据未及时写入文件。

4. **文件指针管理**：
   - 使用 `ftell` 和 `fseek` 精确控制文件指针的位置。
   - 例如，跳过文件的某些部分或追加写入。

---

### **高级示例：读写结构体**

**写入结构体到二进制文件**：
```c
#include <stdio.h>
#include <string.h>

struct Student {
    int id;
    char name[20];
    float score;
};

int main() {
    FILE *fp = fopen("students.bin", "wb");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    struct Student s1 = {1, "Alice", 85.5};
    struct Student s2 = {2, "Bob", 90.0};

    fwrite(&s1, sizeof(struct Student), 1, fp);
    fwrite(&s2, sizeof(struct Student), 1, fp);

    fclose(fp);
    return 0;
}
```

**读取结构体数据**：
```c
#include <stdio.h>

struct Student {
    int id;
    char name[20];
    float score;
};

int main() {
    FILE *fp = fopen("students.bin", "rb");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    struct Student s;
    while (fread(&s, sizeof(struct Student), 1, fp) == 1) {
        printf("ID: %d, Name: %s, Score: %.1f\n", s.id, s.name, s.score);
    }

    fclose(fp);
    return 0;
}
```

---

## 4.定位流

C 标准库提供了一系列用于在文件流中定位和控制位置的函数。这些函数允许你在文件中前后移动文件指针，从而实现随机访问，而不仅仅是顺序读写。

---

### **主要定位函数**

#### **1. `ftell`**  
获取当前文件指针的位置。

**函数原型：**
```c
long ftell(FILE *stream);
```
- **参数**：
  - `stream`：目标文件指针。
- **返回值**：
  - 返回当前文件位置相对于文件开头的字节偏移量。
  - 如果出错，返回 `-1L`，并设置 `errno`。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("example.txt", "r");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    char buffer[10];
    fread(buffer, 1, 5, fp); // 读取 5 个字节
    printf("Current position: %ld\n", ftell(fp));

    fclose(fp);
    return 0;
}
```

---

#### **2. `fseek`**  
设置文件指针的位置。

**函数原型：**
```c
int fseek(FILE *stream, long offset, int whence);
```
- **参数**：
  - `stream`：目标文件指针。
  - `offset`：偏移量（以字节为单位）。
  - `whence`：参考位置：
    - `SEEK_SET`：文件开头。
    - `SEEK_CUR`：当前文件指针位置。
    - `SEEK_END`：文件末尾。

- **返回值**：
  - 成功返回 0，失败返回非零值。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("example.txt", "r");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    // 移动到文件开头之后的第 10 个字节
    fseek(fp, 10, SEEK_SET);
    printf("New position: %ld\n", ftell(fp));

    fclose(fp);
    return 0;
}
```

---

#### **3. `rewind`**  
将文件指针重置到文件开头（等价于 `fseek(stream, 0, SEEK_SET)`）。

**函数原型：**
```c
void rewind(FILE *stream);
```
- **参数**：
  - `stream`：目标文件指针。
- **返回值**：无。
- **说明**：
  - 除了移动指针，还会清除文件流的错误标志（`ferror`）和 EOF 标志（`feof`）。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("example.txt", "r");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    fseek(fp, 10, SEEK_SET); // 移动指针到第 10 个字节
    printf("Position before rewind: %ld\n", ftell(fp));

    rewind(fp); // 重置文件指针
    printf("Position after rewind: %ld\n", ftell(fp));

    fclose(fp);
    return 0;
}
```

---

#### **4. `fgetpos` 和 `fsetpos`**  
存储和恢复文件位置（适合更通用或复杂场景）。

**函数原型：**
```c
int fgetpos(FILE *stream, fpos_t *pos);
int fsetpos(FILE *stream, const fpos_t *pos);
```
- **`fgetpos`**：
  - 获取当前文件指针位置并存储到 `fpos_t` 类型变量中。
- **`fsetpos`**：
  - 将文件指针设置到 `fpos_t` 所保存的位置。

- **参数**：
  - `stream`：目标文件指针。
  - `pos`：文件位置结构。

- **返回值**：
  - 成功返回 0，失败返回非零值。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("example.txt", "r");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    fpos_t pos;
    fread(NULL, 1, 5, fp); // 移动指针 5 字节
    fgetpos(fp, &pos); // 保存当前位置

    printf("Position saved.\n");

    fseek(fp, 10, SEEK_SET); // 移动到其他位置
    fsetpos(fp, &pos); // 恢复到之前保存的位置
    printf("Position restored: %ld\n", ftell(fp));

    fclose(fp);
    return 0;
}
```

---

### **定位函数对比**

| 函数      | 功能                           | 优点                          | 局限性                      |
|-----------|--------------------------------|-------------------------------|-----------------------------|
| `ftell`   | 获取当前位置                   | 简单方便                      | 只适用于字节偏移量           |
| `fseek`   | 设置位置（相对或绝对）          | 精确灵活                      | 不适用于宽字符流或特殊流     |
| `rewind`  | 重置到开头                     | 快捷简单                      | 总是定位到开头，功能有限     |
| `fgetpos` | 保存位置                       | 平台无关，支持复杂流          | 使用稍显繁琐                |
| `fsetpos` | 恢复到保存位置                 | 平台无关，支持复杂流          | 依赖 `fgetpos` 提供的值      |

---

### **常见问题及注意事项**

1. **跨平台字节位置问题**：
   - `ftell` 返回的偏移量是字节数，在文本模式下可能不准确（因不同平台对换行符处理不同）。
   - 在文本文件中避免使用 `fseek` 和 `ftell`。

2. **二进制模式的推荐**：
   - 对于二进制文件，`fseek` 和 `ftell` 提供了精确的定位功能。

3. **`fgetpos` 和 `fsetpos` 的优势**：
   - 适合复杂流（如宽字符流或平台相关流）。
   - 采用了更通用的数据结构（`fpos_t`），避免直接操作字节偏移量。

4. **流错误标志影响**：
   - 文件流进入错误或 EOF 状态时，可能导致定位函数失败。
   - 在使用前可调用 `clearerr` 清除状态标志。

---

## 5.格式化IO

**格式化 I/O** 是 C 标准库的一部分，用于以特定格式输入或输出数据。它在处理结构化文本（如配置文件、日志或用户输入）时非常常用。格式化 I/O 函数分为两类：**格式化输出函数** 和 **格式化输入函数**。

---

### **格式化输出函数**

这些函数用于按照指定格式将数据写入文件或控制台。

#### **1. `printf`**
将格式化字符串输出到标准输出（通常是终端）。

**函数原型：**
```c
int printf(const char *format, ...);
```
- **参数**：
  - `format`：格式字符串。
  - `...`：可变参数，根据格式字符串中的说明符匹配。

- **返回值**：
  - 成功时返回输出的字符数，失败返回负值。

**示例：**
```c
#include <stdio.h>

int main() {
    int age = 25;
    float height = 175.5;
    printf("Age: %d, Height: %.1f cm\n", age, height);
    return 0;
}
```

---

#### **2. `fprintf`**
将格式化字符串输出到指定文件流。

**函数原型：**
```c
int fprintf(FILE *stream, const char *format, ...);
```
- **参数**：
  - `stream`：目标文件指针。
  - `format` 和 `...`：同 `printf`。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("output.txt", "w");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    int value = 100;
    fprintf(fp, "Value: %d\n", value);

    fclose(fp);
    return 0;
}
```

---

#### **3. `sprintf`**
将格式化字符串输出到字符数组。

**函数原型：**
```c
int sprintf(char *str, const char *format, ...);
```
- **参数**：
  - `str`：目标字符数组。
  - `format` 和 `...`：同 `printf`。

**示例：**
```c
#include <stdio.h>

int main() {
    char buffer[50];
    int id = 1234;
    sprintf(buffer, "ID: %04d", id);
    printf("%s\n", buffer);
    return 0;
}
```

---

#### **4. `snprintf`**
类似于 `sprintf`，但指定最大写入字符数以防止缓冲区溢出。

**函数原型：**
```c
int snprintf(char *str, size_t size, const char *format, ...);
```
- **参数**：
  - `str`：目标字符数组。
  - `size`：最大写入字符数（包括终止符）。
  - `format` 和 `...`：同 `printf`。

**示例：**
```c
#include <stdio.h>

int main() {
    char buffer[10];
    int number = 12345;
    snprintf(buffer, sizeof(buffer), "Num: %d", number);
    printf("%s\n", buffer); // 输出 "Num: 12345"
    return 0;
}
```

---

### **格式化输入函数**

这些函数用于按照指定格式从文件或输入流中提取数据。

#### **1. `scanf`**
从标准输入读取格式化数据。

**函数原型：**
```c
int scanf(const char *format, ...);
```
- **参数**：
  - `format`：格式字符串。
  - `...`：变量的地址，用于存储读取到的数据。

- **返回值**：
  - 成功读取的数据项数，遇到 EOF 返回 `EOF`。

**示例：**
```c
#include <stdio.h>

int main() {
    int age;
    printf("Enter your age: ");
    scanf("%d", &age);
    printf("Your age: %d\n", age);
    return 0;
}
```

---

#### **2. `fscanf`**
从指定文件流读取格式化数据。

**函数原型：**
```c
int fscanf(FILE *stream, const char *format, ...);
```
- **参数**：
  - `stream`：文件指针。
  - `format` 和 `...`：同 `scanf`。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("input.txt", "r");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    int value;
    fscanf(fp, "%d", &value);
    printf("Value: %d\n", value);

    fclose(fp);
    return 0;
}
```

---

#### **3. `sscanf`**
从字符串中读取格式化数据。

**函数原型：**
```c
int sscanf(const char *str, const char *format, ...);
```
- **参数**：
  - `str`：输入字符串。
  - `format` 和 `...`：同 `scanf`。

**示例：**
```c
#include <stdio.h>

int main() {
    char input[] = "123 45.67";
    int num;
    float val;

    sscanf(input, "%d %f", &num, &val);
    printf("Number: %d, Value: %.2f\n", num, val);
    return 0;
}
```

---

### **格式说明符**

常见的格式说明符有：
- **整数**：
  - `%d`：十进制整数。
  - `%o`：八进制整数。
  - `%x` 或 `%X`：十六进制整数。
- **浮点数**：
  - `%f`：小数形式。
  - `%e` 或 `%E`：指数形式。
  - `%g` 或 `%G`：自动选择更简洁的表示法。
- **字符和字符串**：
  - `%c`：单个字符。
  - `%s`：字符串（遇空白符结束）。
- **指针和其他**：
  - `%p`：指针地址。
  - `%%`：输出一个 `%`。

---

### **常见问题和注意事项**

1. **缓冲区溢出**：
   - 使用 `snprintf` 替代 `sprintf`。
   - 避免在 `scanf` 中直接使用 `%s`，应指定最大宽度，例如 `%9s`。

2. **输入验证**：
   - 检查函数返回值以确保数据成功读取。
   - 使用 `ferror` 和 `feof` 检查流状态。

3. **数据类型匹配**：
   - 确保格式说明符和变量类型匹配，例如不要用 `%d` 读取 `float`。

4. **宽字符支持**：
   - 对宽字符流，使用对应的宽字符函数如 `wprintf` 和 `wscanf`。

## 6.临时文件

C 标准库提供了对 **临时文件** 的支持，便于程序创建生命周期较短的文件，用于存储中间数据或临时计算结果。这些文件在程序关闭或不再需要时通常会被自动删除。以下是临时文件相关的主要功能。

---

### **临时文件的主要方法**

#### **1. `tmpfile`**
创建一个临时文件，并自动打开以供读写。

**函数原型：**
```c
FILE *tmpfile(void);
```

- **特点**：
  - 文件被创建为二进制模式。
  - 在关闭文件或程序终止时，文件会被自动删除（系统支持时）。
  - 文件仅在当前程序运行期间有效。
  
- **返回值**：
  - 成功时返回 `FILE *` 指针。
  - 如果失败（如权限不足），返回 `NULL`，并设置 `errno`。

**示例：**
```c
#include <stdio.h>

int main() {
    FILE *temp = tmpfile();
    if (temp == NULL) {
        perror("Failed to create temporary file");
        return 1;
    }

    fprintf(temp, "This is a temporary file.\n");
    rewind(temp); // 将文件指针重置到开头

    char buffer[50];
    fgets(buffer, sizeof(buffer), temp);
    printf("Read from temp file: %s", buffer);

    fclose(temp); // 自动删除临时文件
    return 0;
}
```

---

#### **2. `tmpnam`**
生成一个独一无二的临时文件名。

**函数原型：**
```c
char *tmpnam(char *str);
```

- **参数**：
  - `str`：存储生成的临时文件名。如果传入 `NULL`，会返回一个内部静态缓冲区指针。

- **返回值**：
  - 成功时返回临时文件名字符串指针。
  - 如果失败，返回 `NULL`。

- **注意**：
  - 文件名仅是唯一的，但不会实际创建文件，可能会有竞争条件。
  - 不建议在现代多线程程序中使用，因为静态缓冲区可能引发线程安全问题。

**示例：**
```c
#include <stdio.h>

int main() {
    char temp_name[L_tmpnam]; // L_tmpnam 是 tmpnam 可生成的最大长度
    tmpnam(temp_name);

    printf("Temporary file name: %s\n", temp_name);
    return 0;
}
```

---

#### **3. `mkstemp`（非标准，但广泛支持）**
创建一个唯一的临时文件，并以只读写模式打开。

**函数原型**（在 `<stdlib.h>` 或 `<unistd.h>` 中定义）：
```c
int mkstemp(char *template);
```

- **参数**：
  - `template`：一个模板字符串，其中末尾必须包含六个连续的 `'X'`（例如 `"fileXXXXXX"`）。
  
- **返回值**：
  - 成功时返回文件描述符（非 `FILE *` 指针）。
  - 如果失败，返回 `-1`，并设置 `errno`。

- **注意**：
  - 文件会被实际创建。
  - 更安全，避免了 `tmpnam` 的竞争条件问题。

**示例：**
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    char template[] = "tempXXXXXX"; // 必须以 "XXXXXX" 结尾
    int fd = mkstemp(template);
    if (fd == -1) {
        perror("Failed to create temporary file");
        return 1;
    }

    printf("Temporary file created: %s\n", template);

    write(fd, "Hello, temporary file!\n", 23); // 写入内容
    close(fd); // 文件不会自动删除，需手动删除
    unlink(template); // 删除临时文件
    return 0;
}
```

---

### **常见问题和注意事项**

1. **跨平台兼容性**：
   - `tmpfile` 和 `tmpnam` 是标准函数，可跨平台使用。
   - `mkstemp` 是非标准函数，但更安全，建议在 POSIX 环境中使用。

2. **线程安全**：
   - `tmpnam` 使用静态缓冲区，不是线程安全的，建议用 `mkstemp` 替代。

3. **安全性问题**：
   - 如果使用 `tmpnam` 生成文件名，然后手动创建文件，可能存在竞争条件，导致安全隐患。
   - `mkstemp` 避免了此问题，直接创建并打开文件。

4. **清理临时文件**：
   - `tmpfile` 创建的文件会自动删除。
   - 使用 `mkstemp` 创建的文件，需要调用 `unlink` 手动删除。

5. **权限和访问控制**：
   - 临时文件的默认权限依赖于系统的 `umask` 设置。
   - 在创建后可立即调整权限（如 `chmod`），以限制访问。

---


