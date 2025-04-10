# Python基础-流程控制


## 1. 条件判断语句：`if / elif / else`

### 基本结构：

```python
if 条件1:
    执行语句1
elif 条件2:
    执行语句2
else:
    执行语句3
```

### 示例：

```python
age = 20
if age < 18:
    print("未成年人")
elif age < 60:
    print("成年人")
else:
    print("老年人")
```

---


## 2. `match-case`

类似于其他语言中的 `switch-case`：

```python
match fruit:
    case "apple":
        print("apple")
    case "banana":
        print("banana")
    case _:
        print("未知水果")
```

---


