# TypeScript基础-流程控制


## 1. 条件语句

### **`if` 语句**

`if` 语句用于根据条件决定是否执行某段代码。如果条件为 `true`，则执行该代码块。

### 语法：
```ts
if (condition) {
  // 条件为 true 时执行的代码
}
```

### 示例：
```ts
let age = 18;
if (age >= 18) {
  console.log("成年人");
}
```

### **`if...else` 语句**

如果条件不成立，可以使用 `else` 部分执行替代操作。

### 语法：
```ts
if (condition) {
  // 条件为 true 时执行的代码
} else {
  // 条件为 false 时执行的代码
}
```

### 示例：
```ts
let age = 16;
if (age >= 18) {
  console.log("成年人");
} else {
  console.log("未成年人");
}
```

### **`if...else if...else` 语句**

`else if` 可以用于多个条件判断，适用于多个分支的情况。

### 语法：
```ts
if (condition1) {
  // 条件1为 true 时执行的代码
} else if (condition2) {
  // 条件2为 true 时执行的代码
} else {
  // 其他情况
}
```

### 示例：
```ts
let age = 25;
if (age >= 65) {
  console.log("老年人");
} else if (age >= 18) {
  console.log("成年人");
} else {
  console.log("未成年人");
}
```


## 2. `switch` 语句

`switch` 语句用于匹配多个可能的条件，适用于条件值较多的情况。

### 语法：
```ts
switch (expression) {
  case value1:
    // 当 expression 等于 value1 时执行的代码
    break;
  case value2:
    // 当 expression 等于 value2 时执行的代码
    break;
  default:
    // 如果没有匹配到任何值，执行的代码
}
```

### 示例：
```ts
let color = "red";
switch (color) {
  case "red":
    console.log("红色");
    break;
  case "green":
    console.log("绿色");
    break;
  case "blue":
    console.log("蓝色");
    break;
  default:
    console.log("未知颜色");
}
```

### 解释：
- `switch` 根据表达式的值与 `case` 中的值进行匹配，执行相应的代码块。
- `break` 用来跳出 `switch`，防止继续执行后续的 `case` 语句。
- `default` 是一个可选部分，表示当没有匹配的值时执行的代码。

---
