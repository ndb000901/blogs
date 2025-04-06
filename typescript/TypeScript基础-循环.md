# TypeScript基础-循环


## 1. `for` 循环

### 语法：
```ts
for (let i = 0; i < 5; i++) {
  console.log(i);
}
```

## 2. `while` 循环

### 语法：
```ts
while (condition) {
  // 循环体
}
```

### 示例：
```ts
let i = 0;
while (i < 5) {
  console.log(i);  // 输出 0 1 2 3 4
  i++;
}
```

## 3. `do...while` 循环

### 语法：
```ts
do {
  // 循环体
} while (condition);
```

### 示例：
```ts
let i = 0;
do {
  console.log(i);  // 输出 0 1 2 3 4
  i++;
} while (i < 5);
```


## 4. `for...in` 循环

`for...in` 循环用于遍历对象的**可枚举属性**，也可以用于遍历数组的索引。

### 语法：
```ts
for (let key in object) {
  // 循环体
}
```

### 示例：
```ts
const person = { name: "Alice", age: 30, gender: "female" };
for (let key in person) {
  console.log(key, person[key]);  // 输出 name Alice, age 30, gender female
}
```

### 注意：
- `for...in` 循环遍历的是对象的所有可枚举属性，包括继承的属性（通过原型链）。

---

## 5. `for...of` 循环

### 描述：
`for...of` 循环用于遍历可迭代对象（如数组、字符串、Map、Set等）的值。与 `for...in` 不同，`for...of` 遍历的是元素值，而不是索引或属性名。

### 语法：
```ts
for (let value of iterable) {
  // 循环体
}
```

### 示例：
```ts
let arr = [10, 20, 30, 40];
for (let value of arr) {
  console.log(value);  // 输出 10 20 30 40
}
```

### 解释：
- `value` 依次获取数组中的每一个值。
  
### 注意：
- `for...of` 不适用于对象，它只能用于可迭代对象（如数组、字符串等）。

---

## 6. `break` 和 `continue` 语句

### 描述：
- **`break`**：用来**终止循环**，跳出循环体。
- **`continue`**：用来跳过当前迭代，继续下一次循环。

### 示例：
#### 使用 `break`：
```ts
for (let i = 0; i < 5; i++) {
  if (i === 3) {
    break;  // 当 i 为 3 时跳出循环
  }
  console.log(i);  // 输出 0 1 2
}
```

#### 使用 `continue`：
```ts
for (let i = 0; i < 5; i++) {
  if (i === 3) {
    continue;  // 当 i 为 3 时跳过本次循环，继续下一次
  }
  console.log(i);  // 输出 0 1 2 4
}
```

---

## 7. 标签语句

在嵌套循环中，`break` 和 `continue` 默认会影响最内层的循环。通过使用标签（label）语句，可以控制跳出指定的外层循环。

### 语法：
```ts
outerLoop: 
for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    if (j === 2) {
      break outerLoop;  // 跳出 outerLoop 标签指定的外层循环
    }
    console.log(`i = ${i}, j = ${j}`);
  }
}
```

