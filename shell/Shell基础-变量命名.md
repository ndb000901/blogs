# Shell基础-变量命名


## 1. Shell 变量命名的基本规则

1. **只能使用字母（a-z、A-Z）、数字（0-9）和下划线（_）**
2. **不能以数字开头**
3. **不能包含空格**
4. **不能使用特殊字符**（如 `!@#$%^&*()`）
5. **赋值时等号两边不能有空格**

正确示例：
```bash

my_var=123
USER_NAME="Alice"
_server_port=8080
```

错误示例：
```bash

1var=10       # 错误，不能以数字开头
my var=hello  # 错误，不能有空格
my-var=1      # 错误，不能包含 -
```

---

## 2. 变量命名风格建议

### 2.1 **小写加下划线（snake_case）** 

用于普通局部变量、函数内部变量：
```bash

user_name="alice"
backup_dir="/data/backup"
```

### 2.2 **全大写加下划线（UPPER_SNAKE_CASE）** 

用于**常量**、**全局变量**或**环境变量**
```bash

readonly MAX_RETRY=5
export PATH_TO_CONFIG="/etc/myapp/config"
```

### 2.3 **大写字母（单词连写）**
常用于环境变量或系统变量：
```bash

export JAVA_HOME="/usr/lib/jvm/java-11"
```

---

## 3. 变量作用域（命名规范相关）

在函数内声明变量建议加 `local` 修饰，防止污染全局变量：

```bash

my_func() {
  local tmp_file="/tmp/test.log"
  ...
}
```


