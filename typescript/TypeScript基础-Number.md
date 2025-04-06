# TypeScript基础-Number

在 TypeScript 中，`Number` 是一种基本数据类型，用于表示数值。它是 JavaScript 中的数字类型，支持整数和浮点数的表示。`Number` 类型在 TypeScript 中与 JavaScript 的 `number` 类型是完全一致的。

---

## 1. `Number` 基本语法

在 TypeScript 中，`number` 是一个基本数据类型，它包括整数、浮点数、NaN 和 Infinity。可以用数字字面量直接定义 `Number` 类型的变量。

### 创建一个 `Number` 类型变量

```ts
let a: number = 5;    // 整数
let b: number = 3.14; // 浮点数
let c: number = 0b101; // 二进制，等于 5
let d: number = 0o7;   // 八进制，等于 7
let e: number = 0x1F;  // 十六进制，等于 31
```

---

## 2. `Number` 的常见特性

### 2.1 `NaN` (Not-a-Number)

`NaN` 是一种特殊的 `Number` 值，表示“不是一个数字”。它通常出现在数值计算中出现错误时。

```ts
let x = 0 / 0;   // 返回 NaN
console.log(x);   // 输出: NaN
console.log(Number.isNaN(x));  // 输出: true
```

### 2.2 `Infinity` 和 `-Infinity`

`Infinity` 和 `-Infinity` 分别表示正无穷和负无穷。它们在数学计算中表示超出可表示范围的值。

```ts
let positiveInfinity = 1 / 0;   // 正无穷
let negativeInfinity = -1 / 0;  // 负无穷

console.log(positiveInfinity);  // 输出: Infinity
console.log(negativeInfinity);  // 输出: -Infinity
```


## 3. 常用方法

### 3.1 `Number()`

`Number()` 是一个全局函数，用于将其他数据类型转换为数字。如果无法转换，返回 `NaN`。

```ts
let str = "123";
let num = Number(str);  // 字符串转换为数字
console.log(num);        // 输出: 123

let invalid = Number("abc");
console.log(invalid);    // 输出: NaN
```

### 3.2 `parseInt()` 和 `parseFloat()`

- `parseInt()`：将字符串解析为整数。
- `parseFloat()`：将字符串解析为浮点数。

```ts
let strInt = "123px";
let parsedInt = parseInt(strInt);
console.log(parsedInt);  // 输出: 123

let strFloat = "3.14abc";
let parsedFloat = parseFloat(strFloat);
console.log(parsedFloat);  // 输出: 3.14
```

### 3.3 `isNaN()`

`isNaN()` 函数用于检查一个值是否是 `NaN`。但是，注意它会先尝试将传入的值转换为数字。

```ts
console.log(isNaN("123"));   // 输出: false
console.log(isNaN("abc"));   // 输出: true
console.log(isNaN(NaN));     // 输出: true
```

### 3.4 `Number.isNaN()`

与 `isNaN()` 不同，`Number.isNaN()` 只会返回 `true` 当且仅当参数是 `NaN` 本身，不会做隐式类型转换。

```ts
console.log(Number.isNaN("123"));   // 输出: false
console.log(Number.isNaN(NaN));     // 输出: true
console.log(Number.isNaN(123));     // 输出: false
```

### 3.5 `toFixed()` 和 `toPrecision()`

- `toFixed()`：将数字格式化为指定的小数位数，返回字符串。
- `toPrecision()`：将数字格式化为指定的有效数字，返回字符串。

```ts
let num = 3.14159;

console.log(num.toFixed(2));  // 输出: "3.14"
console.log(num.toPrecision(4));  // 输出: "3.142"
```

### 3.6 `toString()` 和 `valueOf()`

- `toString()`：返回数字的字符串表示。
- `valueOf()`：返回数字的原始值。

```ts
let num = 100;

console.log(num.toString());  // 输出: "100"
console.log(num.valueOf());   // 输出: 100
```

---

## 4. 数值范围


在 JavaScript 和 TypeScript 中，`number` 类型是基于 IEEE 754 双精度浮点数标准的。它的有效数字范围大约为：

- 最小值：`-Number.MAX_VALUE`，约为 `-1.79 × 10^308`
- 最大值：`Number.MAX_VALUE`，约为 `1.79 × 10^308`
- 最小可表示的正数：`Number.MIN_VALUE`，约为 `5 × 10^-324`

安全整数范围 (Safe Integer Range):
- 最小值 (Minimum Safe Integer): -2^53 + 1，大约是 -9007199254740991
- 最大值 (Maximum Safe Integer): 2^53 - 1，大约是 9007199254740991

