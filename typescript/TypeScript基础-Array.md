# TypeScript基础-Array

## 1. 数组的基本语法

在 TypeScript 中，数组可以通过两种方式定义：使用方括号 `[]` 或使用 `Array<T>` 泛型。

### 使用方括号 `[]` 定义数组

```ts
let numbers: number[] = [1, 2, 3, 4];
let strings: string[] = ["apple", "banana", "cherry"];
```

### 使用 `Array<T>` 泛型定义数组

```ts
let numbers: Array<number> = [1, 2, 3, 4];
let strings: Array<string> = ["apple", "banana", "cherry"];
```

这两种写法是等效的

---

## 2. 数组的类型注解

数组的类型注解可以更精确地限制数组的内容。你可以指定数组中存储的元素的类型，甚至可以规定数组中的每个元素必须符合一定的格式。

### 同一类型的数组

```ts
let numbers: number[] = [1, 2, 3];  // 只能包含数字类型的元素
```

### 元组类型（不同类型的元素）

在 TypeScript 中，**元组**（Tuple）是数组的一种变体，可以容纳不同类型的元素，元素的数量和类型在编译时是已知的。

```ts
let tuple: [string, number] = ["Alice", 25];  // 第一项是字符串，第二项是数字
```

---

## 3. 数组的方法

### 3.1 `push()`

`push()` 方法用于向数组末尾添加一个或多个元素，并返回新的数组长度。

```ts
let arr: number[] = [1, 2, 3];
arr.push(4);  // 添加数字4
console.log(arr);  // 输出: [1, 2, 3, 4]
```

### 3.2 `pop()`

`pop()` 方法用于删除数组末尾的元素，并返回该元素。

```ts
let arr: number[] = [1, 2, 3, 4];
let popped = arr.pop();  // 删除并返回数组末尾的元素
console.log(popped);  // 输出: 4
console.log(arr);     // 输出: [1, 2, 3]
```

### 3.3 `shift()`

`shift()` 方法用于删除数组的第一个元素，并返回该元素。

```ts
let arr: number[] = [1, 2, 3];
let shifted = arr.shift();  // 删除并返回数组的第一个元素
console.log(shifted);  // 输出: 1
console.log(arr);      // 输出: [2, 3]
```

### 3.4 `unshift()`

`unshift()` 方法用于向数组的开头添加一个或多个元素，并返回新的数组长度。

```ts
let arr: number[] = [2, 3];
arr.unshift(1);  // 在数组开头添加数字1
console.log(arr);  // 输出: [1, 2, 3]
```

### 3.5 `concat()`

`concat()` 方法用于连接两个或多个数组，返回一个新的数组。

```ts
let arr1: number[] = [1, 2];
let arr2: number[] = [3, 4];
let arr3 = arr1.concat(arr2);  // 合并两个数组
console.log(arr3);  // 输出: [1, 2, 3, 4]
```

### 3.6 `slice()`

`slice()` 方法返回数组的一个浅拷贝，提取数组的一部分，不修改原数组。

```ts
let arr: number[] = [1, 2, 3, 4];
let sliced = arr.slice(1, 3);  // 提取从索引1到索引2（不包括索引3）的部分
console.log(sliced);  // 输出: [2, 3]
```

### 3.7 `indexOf()`

`indexOf()` 方法返回指定元素在数组中的第一个索引。如果找不到该元素，返回 `-1`。

```ts
let arr: number[] = [1, 2, 3, 4];
let index = arr.indexOf(3);  // 查找数字3的索引
console.log(index);  // 输出: 2
```

### 3.8 `map()`

`map()` 方法创建一个新数组，数组中的元素是通过调用指定函数处理原数组每个元素后得到的结果。

```ts
let arr: number[] = [1, 2, 3];
let squared = arr.map(x => x * x);  // 对每个元素进行平方处理
console.log(squared);  // 输出: [1, 4, 9]
```

### 3.9 `filter()`

`filter()` 方法创建一个新数组，包含通过测试的所有元素（根据提供的函数）。

```ts
let arr: number[] = [1, 2, 3, 4, 5];
let evenNumbers = arr.filter(x => x % 2 === 0);  // 获取偶数
console.log(evenNumbers);  // 输出: [2, 4]
```

### 3.10 `reduce()`

`reduce()` 方法对数组中的每个元素执行指定的累加操作，返回一个单一的值。

```ts
let arr: number[] = [1, 2, 3, 4];
let sum = arr.reduce((acc, current) => acc + current, 0);  // 求和
console.log(sum);  // 输出: 10
```

---

## 4. 多维数组

TypeScript 也支持多维数组，例如二维数组：

```ts
let matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

console.log(matrix[1][2]);  // 输出: 6
```

---

## 5. 数组的解构赋值

可以使用解构赋值来提取数组中的元素，简化代码。

```ts
let arr: number[] = [1, 2, 3, 4];
let [first, second] = arr;  // 解构赋值
console.log(first);   // 输出: 1
console.log(second);  // 输出: 2
```

---

## 6. 数组的扩展运算符

使用扩展运算符 `...` 可以快速复制数组或合并数组。

### 数组复制

```ts
let arr: number[] = [1, 2, 3];
let newArr = [...arr];  // 复制数组
console.log(newArr);  // 输出: [1, 2, 3]
```

### 合并数组

```ts
let arr1: number[] = [1, 2];
let arr2: number[] = [3, 4];
let merged = [...arr1, ...arr2];  // 合并数组
console.log(merged);  // 输出: [1, 2, 3, 4]
```

---