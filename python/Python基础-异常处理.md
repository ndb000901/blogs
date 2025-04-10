# Python基础-异常处理

## 1. 异常

- 异常是 **程序运行过程中发生的错误**，会导致程序中断。
- 异常发生后，如果没有处理，程序会**崩溃**并显示 traceback。

```python
print(1 / 0)  # ZeroDivisionError: division by zero
```

---

## 2. try-except 语法结构

### 基本格式：

```python
try:
    # 可能出错的代码块
except 异常类型 as e:
    # 异常处理代码
```

### 示例：

```python
try:
    x = int(input("请输入数字："))
except ValueError as e:
    print("输入无效，必须是数字")
```

---

## 3. 多个 except 捕获不同异常

```python
try:
    x = 1 / 0
except ZeroDivisionError:
    print("除以零错误")
except ValueError:
    print("数值错误")
```

---

## 4. 万能捕获（不推荐使用）

```python
try:
    ...
except Exception as e:
    print(f"出错了: {e}")
```

- `Exception` 是所有内建非系统退出类异常的基类
- 仅在你不确定具体错误类型时使用

---

## 5. finally 语句：无论是否异常都执行

```python
try:
    f = open("demo.txt", "r")
except FileNotFoundError:
    print("文件不存在")
finally:
    print("finally 一定会执行")
```

常用于：**资源释放（如文件关闭、网络断开）**

---

## 6. else 语句：try 中没有异常才执行

```python
try:
    x = 5
except Exception:
    print("出错了")
else:
    print("没出错，继续运行")
finally:
    print("清理资源")
```

执行顺序：  
`try → except（如出错） → else（如没错） → finally（总会）`

---

## 7. 自定义异常类

```python
class MyError(Exception):
    def __init__(self, message):
        self.message = message

try:
    raise MyError("这是自定义异常")
except MyError as e:
    print(e.message)
```

---


## 8. 获取异常信息细节

```python
import traceback

try:
    1 / 0
except Exception as e:
    print(type(e))          # <class 'ZeroDivisionError'>
    print(e)                # division by zero
    traceback.print_exc()   # 打印完整 traceback
```

---

## 9. 抛出异常

### 使用 `raise` 抛出异常

```python
raise ValueError("必须是正整数")
```

---

## 10. 嵌套异常处理（try 嵌套 try）

```python
try:
    try:
        1 / 0
    except ZeroDivisionError:
        print("内部除零")
except:
    print("外部异常")
```

---



