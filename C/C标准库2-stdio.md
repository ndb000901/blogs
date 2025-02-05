# C标准库2-stdio

`stdio.h`（**Standard Input Output Header**）是 C 语言标准库中专门用于**文件操作**、**标准输入/输出**和**格式化输入/输出**的头文件。

---

## **1. `stdio.h` 提供的宏**
| 宏 | 说明 |
|----|------|
| `NULL` | 空指针常量（`(void *)0`） |
| `EOF` | 读取文件结束标志（`-1`） |
| `FOPEN_MAX` | 允许同时打开的最大文件数 |
| `BUFSIZ` | `setbuf()` 的默认缓冲区大小 |
| `SEEK_SET` | `fseek()` 定位文件起始位置 |
| `SEEK_CUR` | `fseek()` 当前位置 |
| `SEEK_END` | `fseek()` 文件末尾 |
| `TMP_MAX` | `tmpnam()` 生成的临时文件数量限制 |

---

## **2. `stdio.h` 提供的数据类型**
### **（1）`FILE *`**
- `FILE` 结构体用于表示一个**文件流**。
- `FILE *fp;` 表示一个文件指针，用于操作文件。

### **（2）`fpos_t`**
- 存储文件位置，用于 `fgetpos()` 和 `fsetpos()`。

---

## **3. `stdio.h` 标准输入/输出**
### **（1）标准流**
| 流 | 作用 |
|----|------|
| `stdin` | 标准输入（通常是键盘） |
| `stdout` | 标准输出（通常是终端/屏幕） |
| `stderr` | 标准错误输出（默认输出到终端） |

---

### **（2）格式化输出**
#### **`printf()` 标准输出**
```c
int printf(const char *format, ...);
```
格式化输出到 `stdout`，支持 `%d`、`%f`、`%s` 等格式符号。

```c
printf("整数: %d, 浮点数: %.2f, 字符串: %s\n", 42, 3.14, "Hello");
```

---

#### **`fprintf()` 输出到文件**
```c
int fprintf(FILE *stream, const char *format, ...);
```
类似 `printf()`，但可以输出到文件。

```c
FILE *fp = fopen("test.txt", "w");
fprintf(fp, "写入文件: %d\n", 100);
fclose(fp);
```

---

#### **`sprintf()` 输出到字符串**
```c
int sprintf(char *str, const char *format, ...);
```
将格式化内容存入 `str`，不会输出到 `stdout`。

```c
char buffer[50];
sprintf(buffer, "数值: %d", 123);
```

---

#### **`snprintf()` 限制长度输出到字符串**
```c
int snprintf(char *str, size_t size, const char *format, ...);
```
安全版本，最多写 `size-1` 个字符，确保不会超出数组范围。

```c
char buffer[10];
snprintf(buffer, sizeof(buffer), "太长的字符串会被截断: %d", 12345);
```

---

### **（3）格式化输入**
#### **`scanf()` 标准输入**
```c
int scanf(const char *format, ...);
```
从 `stdin` 读取输入。

```c
int x;
scanf("%d", &x);
```

---

#### **`fscanf()` 从文件读取**
```c
int fscanf(FILE *stream, const char *format, ...);
```
从文件 `stream` 读取数据。

```c
FILE *fp = fopen("test.txt", "r");
int x;
fscanf(fp, "%d", &x);
fclose(fp);
```

---

#### **`sscanf()` 从字符串读取**
```c
int sscanf(const char *str, const char *format, ...);
```
从 `str` 提取数据。

```c
char data[] = "42 3.14 Hello";
int i;
float f;
char s[20];
sscanf(data, "%d %f %s", &i, &f, s);
```

---

## **4. `stdio.h` 文件操作**
### **（1）打开和关闭文件**
#### **`fopen()` 打开文件**
```c
FILE *fopen(const char *filename, const char *mode);
```
| 模式 | 说明 |
|------|------|
| `"r"` | 只读 |
| `"w"` | 只写（清空原内容） |
| `"a"` | 追加 |
| `"r+"` | 读写 |
| `"w+"` | 读写（清空原内容） |
| `"a+"` | 读写（追加） |

```c
FILE *fp = fopen("file.txt", "r");
if (!fp) {
    printf("文件打开失败\n");
    return 1;
}
```

---

#### **`fclose()` 关闭文件**
```c
int fclose(FILE *stream);
```
关闭文件，释放资源。

```c
fclose(fp);
```

---

### **（2）文件读写**
#### **`fgetc()` 读取单字符**
```c
int fgetc(FILE *stream);
```
读取一个字符。

```c
char ch = fgetc(fp);
```

---

#### **`fputc()` 写入单字符**
```c
int fputc(int ch, FILE *stream);
```
写入一个字符。

```c
fputc('A', fp);
```

---

#### **`fgets()` 读取一行**
```c
char *fgets(char *str, int size, FILE *stream);
```
从 `stream` 读取 **最多 `size-1` 个字符**，遇到换行符停止。

```c
char buffer[100];
fgets(buffer, sizeof(buffer), fp);
```

---

#### **`fputs()` 写入字符串**
```c
int fputs(const char *str, FILE *stream);
```
写入字符串，不会自动添加换行。

```c
fputs("Hello, World!\n", fp);
```

---

### **（3）文件定位**
#### **`fseek()` 文件指针移动**
```c
int fseek(FILE *stream, long offset, int origin);
```
- `SEEK_SET`（文件头部）
- `SEEK_CUR`（当前位置）
- `SEEK_END`（文件末尾）

```c
fseek(fp, 0, SEEK_END);  // 移动到文件末尾
```

---

#### **`ftell()` 获取文件位置**
```c
long ftell(FILE *stream);
```
返回当前文件指针位置。

```c
long pos = ftell(fp);
```

---

#### **`rewind()` 复位文件指针**
```c
void rewind(FILE *stream);
```
将文件指针重置到文件开头。

```c
rewind(fp);
```

---

## **5. `stdio.h` 其他实用函数**
### **（1）临时文件**
#### **`tmpfile()` 创建临时文件**
```c
FILE *tmpfile(void);
```
创建一个临时文件，在程序结束时自动删除。

---

#### **`tmpnam()` 生成临时文件名**
```c
char *tmpnam(char *str);
```
返回一个唯一的临时文件名。

```c
char name[L_tmpnam];
tmpnam(name);
printf("临时文件名: %s\n", name);
```

---

## **6. `stdio.h` 重要注意事项**
1. **`fopen()` 返回 `NULL` 时，要用 `perror()` 输出错误信息**。
2. **`fgets()` 读取时要注意字符串溢出问题，必须提供 `size` 限制**。
3. **`printf()` 在某些系统可能不会立即输出，建议 `fflush(stdout);` 立即刷新缓冲区**。
4. **`fseek()` 和 `rewind()` 适用于 `FILE *`，但 `stdin` 不支持 `fseek()`**。

---
