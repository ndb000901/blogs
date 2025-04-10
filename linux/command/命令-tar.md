# tar 命令

`tar`（tape archive）是 Linux 和 Unix 系统中用于创建、维护和解压归档文件的命令。它通常用于将多个文件或目录打包成一个归档文件，以便备份、传输或存档。`tar` 既可以创建归档文件，也可以解压归档文件。

## 1. 基本命令格式

```bash

tar [options] <archive_file> <file_or_directory_to_archive>
```

- `<archive_file>`：指定归档文件的名称（例如 `.tar`、`.tar.gz`、`.tar.bz2` 等）。
- `<file_or_directory_to_archive>`：指定要打包的文件或目录。

## 2. 常用选项

### 2.1 **`-c`**：创建归档文件

```bash

tar -cf archive.tar file1 file2
```
- `-c` 用于创建一个新的归档文件。
- `-f` 后跟归档文件的名称。

### 2.2 **`-x`**：提取归档文件

```bash

tar -xf archive.tar
```
- `-x` 用于从归档文件中提取文件。
- `-f` 后跟归档文件的名称。

### 2.3 **`-v`**：显示详细信息（可选）

```bash

tar -cvf archive.tar file1 file2
```
- `-v`（verbose）选项用于显示命令的详细输出，包括归档过程中涉及的文件。

### 2.4 **`-f`**：指定归档文件
```bash
tar -cf archive.tar directory/
```
- `-f` 用于指定归档文件的名称。该选项必须紧跟在操作符（如 `-c` 或 `-x`）之后，并且归档文件的名称必须紧跟在 `-f` 后面。

### 2.5 **`-z`**：使用 `gzip` 压缩归档

```bash

tar -czf archive.tar.gz directory/
```
- `-z` 选项用于通过 `gzip` 压缩归档文件。`tar` 会创建一个 `.tar.gz` 格式的压缩归档。

### 2.6 **`-j`**：使用 `bzip2` 压缩归档

```bash

tar -cjf archive.tar.bz2 directory/
```
- `-j` 选项用于通过 `bzip2` 压缩归档文件，适合高压缩比的场景，生成 `.tar.bz2` 格式的文件。

### 2.7 **`-J`**：使用 `xz` 压缩归档

```bash

tar -cJf archive.tar.xz directory/
```
- `-J` 选项用于通过 `xz` 压缩归档文件，生成 `.tar.xz` 格式的文件，适合更高的压缩比。

### 2.8 **`-A`**：追加到现有归档

```bash

tar -Af archive.tar additional_files
```
- `-A` 用于将文件追加到现有的归档文件中。

### 2.9 **`-r`**：追加文件到归档

```bash

tar -rf archive.tar newfile
```
- `-r` 用于将新文件追加到现有归档中。归档文件必须是未压缩的 `tar` 格式。

### 2.10 **`-t`**：列出归档内容

```bash

tar -tf archive.tar
```
- `-t` 用于列出归档文件的内容，而不提取文件。

### 2.11 **`-C`**：切换到指定目录

```bash

tar -xvf archive.tar -C /path/to/destination
```
- `-C` 选项允许你在提取文件时指定目标目录。

### 2.12 **`--exclude`**：排除某些文件或目录

```bash

tar -czf archive.tar.gz --exclude='*.log' directory/
```
- `--exclude` 选项允许你排除某些文件或目录。例如，排除所有 `.log` 文件。

### 2.13 **`-p`**：保留文件权限

```bash

tar -cpf archive.tar directory/
```
- `-p` 选项用于保留文件权限，适用于将目录和文件归档并保留原有权限。

### 2.14 **`--strip-components`**：删除归档路径的某些组件
```bash

tar -xvf archive.tar --strip-components=1
```
- `--strip-components=1` 将在提取文件时去掉归档路径中的第一层目录。

