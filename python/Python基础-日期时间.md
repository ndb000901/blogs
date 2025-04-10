# Python基础-日期时间

Python 中的日期和时间处理非常重要，尤其在处理日志、时间戳、时间计算等场景中。Python 提供了丰富的模块和工具来处理日期和时间数据，主要使用 `datetime` 模块和 `time` 模块。

---

## 1. `datetime` 模块

`datetime` 模块是 Python 中用于处理日期和时间的标准模块，提供了很多类和方法来操作日期和时间。

### 1.1 **`datetime` 类**
`datetime` 类是最常用的类之一，表示日期和时间。它有以下常用属性：
- `year`：年份（四位数）
- `month`：月份（1 到 12）
- `day`：日期（1 到 31）
- `hour`：小时（0 到 23）
- `minute`：分钟（0 到 59）
- `second`：秒（0 到 59）
- `microsecond`：微秒（0 到 999999）

#### 示例：

```python
from datetime import datetime

now = datetime.now()  # 获取当前日期和时间
print(now)  # 输出类似：2025-04-10 14:30:00.123456

# 访问属性
print(now.year)  # 输出年份
print(now.month)  # 输出月份
```

### 1.2 **获取当前日期和时间**
```python
from datetime import datetime

# 获取当前日期和时间
now = datetime.now()
print(now)  # 输出：2025-04-10 14:30:00.123456
```

### 1.3 **创建指定日期和时间**
你也可以手动创建 `datetime` 对象，传入年份、月份、日期、小时、分钟、秒等：

```python
from datetime import datetime

dt = datetime(2023, 5, 15, 12, 30, 45)
print(dt)  # 输出：2023-05-15 12:30:45
```

### 1.4 **日期和时间格式化**
`strftime()` 方法将 `datetime` 对象转换为指定格式的字符串。

```python
from datetime import datetime

now = datetime.now()

# 格式化输出
formatted = now.strftime("%Y-%m-%d %H:%M:%S")
print(formatted)  # 输出：2025-04-10 14:30:00
```

常用的格式代码：
- `%Y`：四位数年份
- `%m`：月份（01-12）
- `%d`：日期（01-31）
- `%H`：小时（00-23）
- `%M`：分钟（00-59）
- `%S`：秒（00-59）

### 1.5 **解析字符串为日期和时间**
`strptime()` 方法将字符串解析为 `datetime` 对象。

```python
from datetime import datetime

date_string = "2023-05-15 12:30:45"
dt = datetime.strptime(date_string, "%Y-%m-%d %H:%M:%S")
print(dt)  # 输出：2023-05-15 12:30:45
```

### 1.6 **日期和时间运算**
你可以对 `datetime` 对象进行加减操作，常用的方式是使用 `timedelta` 类。

```python
from datetime import datetime, timedelta

# 当前时间
now = datetime.now()

# 添加 10 天
new_date = now + timedelta(days=10)
print(new_date)

# 减去 5 小时
new_time = now - timedelta(hours=5)
print(new_time)
```

---

## 2. `date` 类

`date` 类表示日期（不包括时间），它具有类似的属性，如年、月、日。

### 2.1 **创建 `date` 对象**
```python
from datetime import date

# 创建指定日期
d = date(2023, 5, 15)
print(d)  # 输出：2023-05-15
```

### 2.2 **获取当前日期**
```python
from datetime import date

# 获取当前日期
today = date.today()
print(today)  # 输出：2025-04-10
```

### 2.3 **日期运算**
可以对 `date` 对象进行加减运算，使用 `timedelta` 类。

```python
from datetime import date, timedelta

today = date.today()

# 添加 10 天
new_date = today + timedelta(days=10)
print(new_date)  # 输出：2025-04-20
```

---

## 3. `time` 类

`time` 类仅表示时间（不包括日期）。它的构造方法支持小时、分钟、秒和微秒。

### 3.1 **创建 `time` 对象**
```python
from datetime import time

# 创建时间对象
t = time(12, 30, 45)
print(t)  # 输出：12:30:45
```

### 3.2 **获取当前时间**
`time` 类本身不提供直接获取当前时间的功能，你通常会从 `datetime.now()` 获取完整的日期时间对象，并提取时间部分。

```python
from datetime import datetime

# 获取当前时间
now = datetime.now()
current_time = now.time()
print(current_time)  # 输出：14:30:00.123456
```

---

## 4. `timedelta` 类

`timedelta` 类用于表示两个 `datetime` 对象之间的差异（即持续时间），或对日期和时间进行加减操作。

### 4.1 **创建 `timedelta` 对象**
```python
from datetime import timedelta

# 创建 timedelta 对象
delta = timedelta(days=5, hours=3, minutes=30)
print(delta)  # 输出：5 days, 3:30:00
```

### 4.2 **日期与 `timedelta` 运算**
`timedelta` 主要用于和 `datetime` 或 `date` 对象进行运算：

```python
from datetime import datetime, timedelta

# 当前日期和时间
now = datetime.now()

# 加上 5 天
new_date = now + timedelta(days=5)
print(new_date)

# 减去 3 小时
new_time = now - timedelta(hours=3)
print(new_time)
```

---

## 5. `timezone` 类

`timezone` 类用于表示带有时区信息的时间。Python 3.9+ 提供了对时区的支持，允许你在 `datetime` 中处理时区信息。

### 5.1 **创建 `timezone` 对象**
```python
from datetime import timezone, timedelta

# 创建 UTC+2 的时区对象
tz = timezone(timedelta(hours=2))
print(tz)  # 输出：UTC+02:00
```

### 5.2 **与时区相关的日期时间**
可以将时区信息附加到 `datetime` 对象上：

```python
from datetime import datetime, timezone, timedelta

# 创建带时区的日期时间对象
dt = datetime(2023, 5, 15, 12, 30, 45, tzinfo=timezone(timedelta(hours=2)))
print(dt)  # 输出：2023-05-15 12:30:45+02:00
```

---

## 6. `time` 模块

`time` 模块提供了对时间相关功能的支持，尤其是在需要处理 Unix 时间戳或进行时间延迟等操作时非常有用。

### 6.1 **获取当前时间戳**
`time.time()` 返回当前时间的时间戳，即自 1970 年 1 月 1 日（UNIX 纪元）以来的秒数。

```python
import time

timestamp = time.time()
print(timestamp)  # 输出类似：1650000000.123456
```

### 6.2 **时间格式化与解析**
`time.strftime()` 和 `time.strptime()` 用于时间的格式化和解析。

```python
import time

# 当前时间戳
current_time = time.localtime()

# 格式化时间
formatted_time = time.strftime("%Y-%m-%d %H:%M:%S", current_time)
print(formatted_time)  # 输出：2025-04-10 14:30:00

# 解析时间字符串
time_string = "2023-05-15 12:30:45"
parsed_time = time.strptime(time_string, "%Y-%m-%d %H:%M:%S")
print(parsed_time)  # 输出：time.struct_time(tm_year=2023, tm_mon=5, tm_mday=15, tm_hour=12, tm_min=30, tm_sec=45, tm_wday=0, tm_yday=135, tm_isdst=-1)
```

### 6.3 **时间延迟**
`time.sleep()` 用于使程序暂停执行一定时间。

```python
import time

print("Start")
time.sleep(2)  # 暂停 2 秒
print("End")
```

---
