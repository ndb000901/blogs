# TypeScript基础-基本数据类型


| 数据类型      | 描述                                                      | 示例                         | 备注                                     |
| ------------- | --------------------------------------------------------- | ---------------------------- | ---------------------------------------- |
| `number`      | 表示所有数字类型，包括整数和浮点数。                    | `let age: number = 30;`      | 无论是整数还是浮点数，都是 `number` 类型。 |
| `string`      | 表示字符串类型，存储文本数据。                            | `let name: string = "Alice";` | 支持单引号、双引号和模板字符串（反引号）。 |
| `boolean`     | 表示布尔类型，只有两个值：`true` 或 `false`。             | `let isActive: boolean = true;` | 用于逻辑判断和条件判断。                |
| `null`        | 表示空值，通常用于表示对象为空。                          | `let value: null = null;`    | `null` 是一个独立的数据类型。            |
| `undefined`   | 表示变量未被初始化或未赋值。                             | `let notDefined: undefined;` | `undefined` 是一个独立的数据类型。     |
| `symbol`      | 表示唯一的、不可变的标识符。用于对象的唯一属性键。        | `const sym = Symbol("id");`  | 每个 `symbol` 是唯一的。                 |
| `bigint`      | 表示大整数类型，用于表示非常大的整数。                    | `let bigNum: bigint = 9007199254740991n;` | 通过 `n` 后缀或 `BigInt()` 构造函数表示。 |



## 1. `number`— 数字类型

- **描述**：`number` 类型表示所有的数字，包括整数和浮点数。
- **支持的值**：整型、浮点型、特殊数值（如 `NaN`、`Infinity`）。

### 示例：
```ts
let age: number = 30;
let price: number = 9.99;
let notANumber: number = NaN;  // 特殊数值
let infinity: number = Infinity;  // 特殊数值
```

### 注意：
- JavaScript 和 TypeScript 中的 `number` 没有区分整数和浮点数，它们都属于 `number` 类型。
- 对于大数值，TypeScript 默认支持 `number` 类型表示，但对于超出最大表示范围的数值（如超过 `2^53 - 1`），可能会出现精度丢失。

---

## 2. `string` — 字符串类型

- **描述**：`string` 类型表示一系列字符的集合（即字符串）。字符串是不可变的，也就是每次修改字符串时都会创建一个新字符串。
- **可以使用单引号、双引号或反引号来定义字符串**，反引号支持模板字符串（Template Literals）。

### 示例：
```ts
let name: string = "Alice";
let greeting: string = 'Hello, world!';
let message: string = `Hello, ${name}!`;  // 模板字符串
```

---

## 3. `boolean` — 布尔类型

- **描述**：`boolean` 类型表示 `true` 或 `false`，用于逻辑运算和条件判断。
- **布尔类型只有两个值**：`true` 和 `false`。

### 示例：
```ts
let isActive: boolean = true;
let isCompleted: boolean = false;
```

---

## 4. `null` 和 `undefined` — 空值和未定义类型

- **`null`**：表示没有值，通常用于表示空对象引用。
- **`undefined`**：表示变量未被初始化或未赋值的状态，默认情况下，声明的变量会被赋值为 `undefined`。

### 示例：
```ts
let a: null = null;
let b: undefined = undefined;

let x: string | undefined;  // x 被隐式赋值为 undefined
```

### 注意：
- 在 TypeScript 中，`null` 和 `undefined` 是不同的类型。若启用了 `strictNullChecks`，它们不能互相赋值。
- 在启用了 `strictNullChecks` 的情况下，`null` 只能赋值给 `null` 类型，`undefined` 只能赋值给 `undefined` 类型。

---

## 5. `symbol` — 符号类型

- **描述**：`symbol` 是一种新的基本数据类型，表示一个唯一的、不可变的标识符。主要用于对象属性的键，确保键的唯一性。
- **用途**：常用于避免对象属性的命名冲突。

### 示例：
```ts
const uniqueSymbol: symbol = Symbol('unique');
const obj = {
  [uniqueSymbol]: 'value'
};

console.log(obj[uniqueSymbol]);  // 输出: value
```

### 注意：
- `symbol` 类型主要用于高级编程模式，如创建独一无二的属性键。

---

## 6. `bigint` — 大整数类型

- **描述**：`bigint` 是一种可以表示任意大小整数的类型，超过了 `number` 类型的最大值范围（`2^53 - 1`）。
- **用途**：用于处理非常大的整数值，如金融计算、加密算法等。

### 示例：
```ts
let bigIntValue: bigint = 9007199254740991n;  // 注意后缀 n
let anotherBigInt: bigint = BigInt(12345678901234567890);
```

### 注意：
- `bigint` 类型只能用于表示整数，不能用于小数。
- 在使用 `bigint` 时，必须在数字后添加 `n` 后缀，或使用 `BigInt()` 函数。

---

## 7. 类型推导与联合类型（`|`）

- **类型推导**：TypeScript 支持根据赋值自动推导变量的类型。比如：
```ts
let num = 42;  // 推导为 number 类型
let text = "Hello";  // 推导为 string 类型
```

- **联合类型（`|`）**：表示变量可以是多种类型之一。
```ts
let value: string | number = 42;
value = "Hello";  // 合法
```

---

## 8. 类型别名（`type`）与字面量类型

- **字面量类型**：可以指定特定的值，而不仅仅是某个类型。例如，允许一个变量只接受特定字符串或数字。
```ts
type Direction = "left" | "right";
let move: Direction = "left";  // 合法
move = "up";  // 错误
```

- **类型别名（`type`）**：可以创建自定义类型。
```ts
type Age = number;
let myAge: Age = 30;
```

---

