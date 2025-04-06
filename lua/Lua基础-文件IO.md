# Lua基础-文件IO


Lua 的 **文件 IO（输入输出）** 提供了对文件的读取、写入和关闭操作的强大而简洁的支持，主要依赖标准库 `io` 和 `file` 对象。


---

## 1. 文件打开方式（`io.open(path, mode)`）

| 模式 | 含义 |
|------|------|
| `"r"`  | 只读，文件必须存在 |
| `"w"`  | 写入，若存在则清空 |
| `"a"`  | 追加，文件指针移动到末尾 |
| `"r+"` | 读写，文件必须存在 |
| `"w+"` | 读写，若存在则清空 |
| `"a+"` | 读写，指针在末尾，不能修改中间内容 |
| `"b"`  | 二进制模式（可与上面组合，如 `"rb"`）|


## 2. 写文件

```lua
local file = io.open("test.txt", "w")  -- 打开或创建文件写入
file:write("Hello Lua!\n")
file:write("第二行\n")
file:close()
```

---

## 3. 读文件

### 3.1 一次读一行

```lua
local file = io.open("test.txt", "r")
for line in file:lines() do
  print(line)
end
file:close()
```

### 3.2 一次读取整个内容

```lua
local file = io.open("test.txt", "r")
local content = file:read("*a")  -- 读取所有内容
print(content)
file:close()
```

### 3.3 读取一行 / 一个数字 / 一段字节

```lua
file = io.open("test.txt", "r")
print(file:read("*l"))    -- 读一行
print(file:read("*n"))    -- 读一个数字
print(file:read(10))      -- 读10个字节
file:close()
```

---

## 4. 关闭文件

```lua
file:close()
```

Lua 在垃圾回收时也会自动关闭文件，但**推荐手动关闭**。

---

## 5. 使用 `io.lines`

```lua
for line in io.lines("test.txt") do
  print(line)
end
```
