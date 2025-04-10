# Python基础-创建项目

## 1. 虚拟环境

虚拟环境（Virtual Environment）是 Python 中用来隔离项目依赖的工具。
它可以让每个项目使用不同版本的 Python 解释器和库，而不会相互干扰。


## 2. 新建项目

```bash

# 1.新建目录
mkdir demo

# 2.创建虚拟环境，这里使用我自己编译的版本，venv 是目录名(最后的venv)，用于存放虚拟环境的资源
/home/hello/local/python3.13.2/bin/python3 -m venv venv

# 或使用 python3 -m venv venv3

# 3.激活环境
source venv/bin/activate  # 激活 (Linux/macOS)

.\venv\Scripts\activate  # 激活 (Windows)
```

## 3. 退出虚拟环境

```bash

deactivate
```

### `which deactivate`

```bash

# which deactivate

# 输出
deactivate () {
        if [ -n "${_OLD_VIRTUAL_PATH:-}" ]
        then
                PATH="${_OLD_VIRTUAL_PATH:-}" 
                export PATH
                unset _OLD_VIRTUAL_PATH
        fi
        if [ -n "${_OLD_VIRTUAL_PYTHONHOME:-}" ]
        then
                PYTHONHOME="${_OLD_VIRTUAL_PYTHONHOME:-}" 
                export PYTHONHOME
                unset _OLD_VIRTUAL_PYTHONHOME
        fi
        hash -r 2> /dev/null
        if [ -n "${_OLD_VIRTUAL_PS1:-}" ]
        then
                PS1="${_OLD_VIRTUAL_PS1:-}" 
                export PS1
                unset _OLD_VIRTUAL_PS1
        fi
        unset VIRTUAL_ENV
        unset VIRTUAL_ENV_PROMPT
        if [ ! "${1:-}" = "nondestructive" ]
        then
                unset -f deactivate
        fi
}
```