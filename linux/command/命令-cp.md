# cp 命令

`cp`（copy）是 Linux 和 Unix 系统中用来复制文件和目录的命令。它可以复制文件到指定位置，也可以复制目录及其内容。

## 1. 基本命令格式

```bash

cp [options] <source> <destination>
```

- `<source>`：源文件或目录，可以是一个文件、多个文件或目录。
- `<destination>`：目标文件或目录，可以是一个文件或目录。如果目标是目录，`cp` 会将源文件复制到该目录中。

## 2. 常用选项
`cp` 命令有许多选项，可以用来定制复制的行为。以下是一些常用的选项：

### 2.1 **`-i`**：交互模式

```bash

cp -i <source> <destination>
```

- 在目标文件已存在时，`-i` 会提示用户确认是否覆盖目标文件。需要用户输入 `y` 确认覆盖，`n` 表示不覆盖。

### 2.2 **`-r` 或 `-R`**：递归复制（用于目录）

```bash

cp -r <source_directory> <destination_directory>
```
- `-r` 或 `-R` 选项用于递归复制目录及其内容，包括子目录和文件。如果要复制目录及其下所有内容，必须使用这个选项。

### 2.3 **`-u`**：只复制源文件比目标文件更新的文件

```bash

cp -u <source> <destination>
```
- 只在源文件比目标文件更新时才进行复制。如果目标文件已是最新的，`cp` 不会进行复制。

### 2.4 **`-v`**：显示详细输出

```bash

cp -v <source> <destination>
```
- `-v` 选项会使 `cp` 在复制时显示每个文件的详细信息，包括源文件和目标文件路径。

### 2.5 **`-f`**：强制复制

```bash

cp -f <source> <destination>
```
- `-f` 会强制复制文件，若目标文件已存在且不可写，则会删除该文件后再复制。

### 2.6 **`-a`**：归档模式

```bash

cp -a <source> <destination>
```
- `-a` 是 `cp` 的归档模式，等同于 `-dR --preserve=all`，它会递归地复制目录并尽可能保留文件属性（如文件的时间戳、权限、符号链接等）。适合用于完整备份。

### 2.7 **`-p`**：保留文件属性

```bash

cp -p <source> <destination>
```
- `-p` 会保留源文件的时间戳、权限等文件属性，适用于希望复制文件而不改变文件元数据的场景。

### 2.8 **`-l`**：创建硬链接而非复制

```bash

cp -l <source> <destination>
```
- `-l` 创建硬链接，而不是复制文件。硬链接是指向同一个文件数据块的多个文件名。

### 2.9 **`--no-clobber`**：不覆盖已存在的文件

```bash

cp --no-clobber <source> <destination>
```
- `--no-clobber` 用来防止 `cp` 覆盖已存在的文件。如果目标文件已存在，`cp` 将不会执行复制操作。

### 2.10 **`-T`**：目标为文件而非目录

```bash

cp -T <source> <destination>
```
- `-T` 强制将目标作为普通文件来处理，而不是目录，即使目标是目录，`cp` 会认为目标是一个文件并直接复制。

### 2.11 **`--backup`**：备份已覆盖的文件

```bash

cp --backup <source> <destination>
```
- `--backup` 选项会在覆盖文件之前先备份目标文件。备份的文件会有一个 `~` 后缀，或者根据具体的备份策略有所不同。

### 2.12 **`--remove-destination`**：删除目标文件后再复制

```bash

cp --remove-destination <source> <destination>
```
- `--remove-destination` 会在目标文件已经存在时，先删除目标文件再进行复制。

