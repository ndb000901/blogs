# Lua基础-协同程序


Lua 中的 **协同程序（coroutine）** 是一套非常强大的机制，可以实现**协作式多任务**，允许你**挂起与恢复函数的执行**。它在某种程度上类似于轻量线程，但它不是并发执行，而是**手动切换控制权**。

简单来说，协同程序是 Lua 中的一种 **可以中断并从中断处继续运行的函数**，通过 `coroutine.create` 创建，通过 `coroutine.resume` 启动，通过 `coroutine.yield` 暂停。

---

## 1. 核心 API

| 函数名                 | 描述 |
|------------------------|------|
| `coroutine.create(f)`  | 创建一个协同程序 |
| `coroutine.resume(co)` | 启动或继续协同程序 |
| `coroutine.yield(...)` | 暂停协同程序并返回值给 resume |
| `coroutine.status(co)` | 获取状态：`suspended`, `running`, `dead` |
| `coroutine.wrap(f)`    | 创建一个函数式的协程接口 |

---

## 2. 示例

### 2.1 创建与执行

```lua
function foo()
  print("1. 协程开始")
  coroutine.yield()  -- 暂停协程
  print("2. 协程继续")
end

co = coroutine.create(foo)

coroutine.resume(co)  -- 输出: 1. 协程开始
coroutine.resume(co)  -- 输出: 2. 协程继续
```

---

## 3. `yield` 与 `resume` 参数传递

```lua
function worker()
  local input = coroutine.yield("等待输入")
  print("收到：", input)
end

co = coroutine.create(worker)

local ok, msg = coroutine.resume(co)
print("返回：", msg)  --> 返回：等待输入

coroutine.resume(co, "Hello coroutine")  --> 收到：Hello coroutine
```

- 第一次 `resume` 启动协程；
- `yield` 返回的值是传给 `resume` 的返回值；
- 下一次 `resume(...)` 的参数会传入 `yield(...)` 处，继续运行。

---

## 4. 状态说明

```lua
print(coroutine.status(co)) --> dead（已结束）
```

状态包括：

- `suspended`：可 resume
- `running`：当前在执行
- `normal`：resume 的调用栈内部
- `dead`：运行结束或出错

---

## 5. wrap 简化写法

```lua
co = coroutine.wrap(function()
  print("Hello")
  coroutine.yield()
  print("World")
end)

co()  --> Hello
co()  --> World
```

`coroutine.wrap` 会返回一个**函数**，调用它等同于 `resume`，但是不会返回状态（错误会直接抛出）。

---


