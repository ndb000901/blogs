# TypeScript基础-String

在 TypeScript 中，`String` 是一种常用的数据类型，用于表示文本数据。与 JavaScript 中的 `string` 类型一样，`String` 类型的变量可以包含字符、数字或符号等信息。它是不可变的，一旦创建，字符串中的内容就不能被改变。

---

## 1. `String` 基本语法

在 TypeScript 中，`string` 类型用于表示一串字符。你可以使用单引号、双引号或反引号来定义字符串。

### 创建 `String` 类型的变量

```ts
let str1: string = "Hello, TypeScript!";  // 使用双引号
let str2: string = 'Hello, TypeScript!';  // 使用单引号
let str3: string = `Hello, TypeScript!`;  // 使用反引号 (模板字面量)
```

---

## 2. 字符串的创建与表示方式

### 1. **字符串字面量**

可以使用单引号 `'` 或双引号 `"` 来表示字符串字面量：

```ts
let message: string = "Welcome to TypeScript!";
let greeting: string = 'Hello!';
```

### 2. **模板字面量**

模板字面量是使用反引号 `` ` `` 来表示字符串，并且支持内嵌表达式。可以通过 `${}` 插入变量或表达式：

```ts
let name: string = "John";
let greeting: string = `Hello, ${name}!`;  // 输出: Hello, John!
```

### 3. **多行字符串**

使用模板字面量（反引号）时，可以轻松创建多行字符串：

```ts
let multiLineStr: string = `This is line 1
This is line 2
This is line 3`;

console.log(multiLineStr);
// 输出:
// This is line 1
// This is line 2
// This is line 3
```

---

## 3. 常用方法

### 3.1 **`length`**

`length` 属性返回字符串的长度（字符数）。

```ts
let str = "TypeScript";
console.log(str.length);  // 输出: 10
```

### 3.2 **`toUpperCase()` 和 `toLowerCase()`**

- `toUpperCase()`：返回字符串的一个副本，将所有字符转换为大写字母。
- `toLowerCase()`：返回字符串的一个副本，将所有字符转换为小写字母。

```ts
let str = "TypeScript";
console.log(str.toUpperCase());  // 输出: TYPESCRIPT
console.log(str.toLowerCase());  // 输出: typescript
```

### 3.3 **`charAt()` 和 `charCodeAt()`**

- `charAt(index)`：返回指定位置的字符。
- `charCodeAt(index)`：返回指定位置字符的 Unicode 编码。

```ts
let str = "TypeScript";
console.log(str.charAt(0));  // 输出: T
console.log(str.charCodeAt(0));  // 输出: 84 (字符 'T' 的 Unicode 编码)
```

### 3.4 **`indexOf()` 和 `lastIndexOf()`**

- `indexOf(substring)`：返回子字符串首次出现的位置，如果没有找到返回 `-1`。
- `lastIndexOf(substring)`：返回子字符串最后一次出现的位置。

```ts
let str = "TypeScript is great";
console.log(str.indexOf("is"));  // 输出: 10
console.log(str.lastIndexOf("is"));  // 输出: 10
```

### 3.5 **`slice()` 和 `substring()`**

- `slice(startIndex, endIndex)`：提取从 `startIndex` 到 `endIndex` 之间的字符（不包括 `endIndex`）。
- `substring(startIndex, endIndex)`：与 `slice` 类似，但 `startIndex` 和 `endIndex` 位置调换时，`substring` 会交换它们。

```ts
let str = "TypeScript";
console.log(str.slice(0, 4));  // 输出: Type
console.log(str.substring(0, 4));  // 输出: Type
```

### 3.6 **`replace()`**

`replace()` 方法用于替换字符串中的某个子字符串。它返回一个新字符串，原字符串不变。

```ts
let str = "TypeScript is fun";
let newStr = str.replace("fun", "awesome");
console.log(newStr);  // 输出: TypeScript is awesome
```

### 3.7 **`split()`**

`split()` 方法将字符串分割成数组，返回一个新数组。可以指定分隔符（字符或正则表达式）。

```ts
let str = "TypeScript, JavaScript, Python";
let arr = str.split(", ");
console.log(arr);  // 输出: ['TypeScript', 'JavaScript', 'Python']
```

### 3.8 **`trim()`**

`trim()` 方法移除字符串两端的空白字符（包括空格、制表符等）。

```ts
let str = "  TypeScript  ";
console.log(str.trim());  // 输出: TypeScript
```

### 3.9 **`includes()`**

`includes()` 方法检查字符串中是否包含某个子字符串。如果包含，返回 `true`，否则返回 `false`。

```ts
let str = "TypeScript is awesome";
console.log(str.includes("awesome"));  // 输出: true
console.log(str.includes("java"));    // 输出: false
```

---

## 4. 字符串模板与插值

模板字面量不仅可以处理多行字符串，还允许内嵌表达式。它是一种非常强大的工具，通常用于动态生成字符串。

### 插入变量或表达式

```ts
let firstName = "John";
let lastName = "Doe";
let greeting = `Hello, my name is ${firstName} ${lastName}.`;
console.log(greeting);  // 输出: Hello, my name is John Doe.
```

### 多行模板字符串

```ts
let multiLine = `This is line 1
This is line 2
This is line 3`;
console.log(multiLine);
// 输出:
// This is line 1
// This is line 2
// This is line 3
```

---

## 5. `String` 的类型判断与转换

### `String` 类型转换

`String()` 是一个全局函数，将其他类型的值转换为字符串。

```ts
let num = 123;
let str = String(num);
console.log(str);  // 输出: "123"
```

### `typeof` 操作符

`typeof` 用于检查一个值的类型，如果值是字符串，返回 `"string"`。

```ts
let str = "Hello, TypeScript!";
console.log(typeof str);  // 输出: string
```

