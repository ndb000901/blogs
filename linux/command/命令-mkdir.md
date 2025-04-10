# mkdir 命令

`mkdir`（make directory）是一个用于创建新目录的命令。它是 Linux 和 Unix 系统中最基本的命令之一，广泛用于文件系统中创建新的目录。

## 1. 基本命令格式

```bash

mkdir [options] <directory_name>
```
- `<directory_name>`：要创建的新目录的名称。如果指定的路径中包含多个目录，`mkdir` 会根据需要创建所有目录。

## 2. 常用选项

### 2.1 **`-p`**：创建父目录

```bash

mkdir -p /path/to/directory
```
- 使用 `-p` 选项时，如果中间的父目录不存在，`mkdir` 会自动创建这些目录。即使父目录已经存在，`mkdir -p` 也不会报错。这对于创建嵌套目录特别有用。

**示例**：
```bash

mkdir -p /home/user/projects/myproject
```
此命令将创建 `/home/user/projects/myproject` 目录。如果 `/home/user/projects` 不存在，`mkdir` 会首先创建 `/home/user/projects`，然后再创建 `myproject`。

### 2.2 **`-v`**：显示详细信息

```bash

mkdir -v <directory_name>
```
- 使用 `-v` 选项时，`mkdir` 会输出创建的目录信息。这对于调试和验证操作非常有用。

**示例**：
```bash

mkdir -v new_directory
```
此命令会输出类似 `mkdir: created directory 'new_directory'` 的信息，表示创建了该目录。

### 2.3 **`-m`**：设置目录权限

```bash

mkdir -m 755 <directory_name>
```
- 使用 `-m` 选项可以在创建目录时设置该目录的权限。权限是以数字表示的，类似于 `chmod` 命令中使用的权限设置。

**示例**：
```bash

mkdir -m 700 secret_directory
```
此命令会创建一个名为 `secret_directory` 的目录，并将其权限设置为 `700`，表示只有文件的所有者有读、写、执行权限，其他用户没有任何权限。

### 2.4 **`--mode`**：与 `-m` 类似，用于设置权限

```bash

mkdir --mode=755 <directory_name>
```
- `--mode` 是 `-m` 的长格式，它用于设置创建目录的权限，行为与 `-m` 相同。

### 2.5 **`--help`**：查看帮助信息

```bash

mkdir --help
```
- 显示 `mkdir` 命令的帮助信息，列出所有支持的选项和用法。

### 2.6 **`--parents`**：创建多级目录

- `--parents` 选项和 `-p` 一样，也允许你在父目录不存在的情况下创建多级目录。


## 3. 目录权限

- `mkdir` 会根据用户的 umask 设置来决定目录的默认权限。
    - 如果不使用 `-m` 选项，`mkdir` 默认创建权限为 `775`（即用户和组具有读、写、执行权限，其他用户具有读和执行权限）。
    - 使用 `-m` 选项可以指定创建目录时的权限。

