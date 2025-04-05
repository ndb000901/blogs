# Shell基础-文件包含

在 Shell 脚本中，**文件包含**（file inclusion）允许一个脚本将另一个脚本的内容嵌入到当前脚本中。这种机制有助于代码复用、模块化、提高可维护性和清晰性。

在 Shell 中，文件包含的常用方式有两种：`source` 和 `.`（点命令）。

---

## `source` 命令

`source` 是一种将一个脚本文件的内容加载到当前 shell 环境中的方法。通过这种方式，脚本中的变量、函数等会被当前脚本直接使用，而无需启动新进程。

### 语法

```bash

source filename
```

或者使用简写：

```bash

. filename
```

### 使用场景

- 在当前 shell 环境中执行另一个脚本。
- 需要共享脚本中的变量、函数等，而不想创建新进程。

### 示例

假设有一个 `config.sh` 脚本，里面定义了一些变量和函数：

```bash

# config.sh
var1="Hello"
var2="World"

print_message() {
  echo "$var1 $var2"
}
```

然后在主脚本中通过 `source` 或 `.` 命令加载该脚本：

```bash

# main.sh
source config.sh
# 或者
. config.sh

print_message  # 输出：Hello World
```

通过 `source` 或 `.` 命令，`config.sh` 文件中的变量和函数会被加载到当前 shell 环境中。主脚本可以直接使用 `config.sh` 中定义的变量和函数。

### 注意事项

- 使用 `source` 或 `.` 命令时，脚本中的内容会在当前 shell 环境中执行，这意味着脚本中的所有修改（如变量赋值）都将在当前 shell 会话中生效。
- 如果被 `source` 或 `.` 的文件发生错误，当前脚本不会退出，脚本会继续执行。

---

