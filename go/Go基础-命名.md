# Go基础-命名


在 Go 语言中，**标识符命名（Identifier Naming）** 是非常重要的，不仅影响代码可读性，也关系到包、变量、函数的可见性（即是否对外暴露）。


## 1. 命名规则

### 合法命名规则
- **只能包含字母（Unicode）+ 数字 + 下划线**
- **不能以数字开头**
- **不能使用 Go 的关键字**

合法示例：

```go
userName, age, HTTPServer, max_value
```

不合法示例：

```go
1abc, var, go-func, total$sum // 错误命名
```

---

## 2. 命名风格规范

Go 提倡 **简洁、直白、约定俗成的命名方式**：

### 2.1 **包名：全部小写，不使用下划线**

```go
package mathutils  // 推荐
package MathUtils  // 不推荐
package math_utils // 不推荐
```

> 一般用包名来表示功能，如 `net/http`, `encoding/json`, `os`, `io`, `strings` 等。

---

### 2.2 **变量/函数：驼峰命名法（camelCase）**

```go
var userName string
func getUserName() string
```

- 不使用下划线（Go 不推荐 `snake_case`）
- 函数名应为动词或动词短语，如 `openFile()`, `sendMessage()`

---

### 2.3 **类型名（struct / interface）：大驼峰（PascalCase）**

```go
type UserInfo struct {
	UserID   int
	UserName string
}
```

> 所有结构体、接口、枚举名使用大写开头，遵循 Go 公共导出规范。

---

### 2.4 **常量：驼峰命名（不建议全大写）**

```go
const maxUserLimit = 100  // 推荐
const MAX_USER_LIMIT = 100  // Go 风格不推荐
```

> 与 C/C++ 不同，Go 中常量一般仍采用驼峰风格，而非全大写。

---

## 3. 大小写与可见性（Exported / Unexported）

| 名称首字母 | 可见性 | 说明 |
|------------|--------|------|
| 大写       | 公有（可导出） | 其他包可以访问 |
| 小写       | 私有（包内）   | 只能在本包内使用 |

```go
type User struct {}     // 公有类型
func GetUser() {}       // 公有函数
func getToken() {}      // 私有函数
var maxValue int        // 私有变量
var MaxValue int        // 公有变量
```

