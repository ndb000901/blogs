# Lua基础-垃圾回收


## 1. 手动控制 GC（`collectgarbage` 函数）

Lua 提供了标准库函数 `collectgarbage` 来操作 GC：

```lua
collectgarbage("collect")       -- 手动启动一次完整 GC
collectgarbage("stop")          -- 停止自动 GC
collectgarbage("restart")       -- 重新启动自动 GC
collectgarbage("count")         -- 返回当前内存使用（单位 KB）
collectgarbage("step", N)       -- 步进执行 N 单位 GC
collectgarbage("isrunning")     -- 是否在自动 GC
collectgarbage("setpause", N)   -- 设置间歇系数（默认 200）
collectgarbage("setstepmul", N) -- 设置步进倍数（默认 100）
```

示例：

```lua
print(collectgarbage("count")) -- 打印当前内存使用
collectgarbage("collect")      -- 立刻启动一次完整 GC
```

---

## 2. GC 的调优参数（暂停和步进倍数）

通过下面两个参数控制 GC 的频率和激进程度：

| 参数         | 默认值 | 含义                                                         |
|--------------|--------|--------------------------------------------------------------|
| `pause`      | 200    | 当前内存 > 上次内存 × (pause / 100) 时触发 GC               |
| `stepmul`    | 100    | 每次 GC 步进使用的 CPU 时间比例，越大回收越积极             |

例如：

```lua
collectgarbage("setpause", 100)     -- 提高 GC 频率
collectgarbage("setstepmul", 200)   -- 更激进地执行 GC 步进
```



