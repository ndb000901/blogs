# TypeScript基础-联合类型


## 1. 联合类型的基本语法

联合类型的基本语法形式如下：

```ts
type VariableName = Type1 | Type2 | Type3;
```

- `|`：竖线符号用于表示“或”的关系，即变量可以是多种类型中的一种。

### 示例：

```ts
let value: number | string;

value = 42;       // 合法
value = "Hello";   // 合法
value = true;      // 错误，`true` 不是 `number` 或 `string` 类型
```
