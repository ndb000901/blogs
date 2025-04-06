# TypeScript基础-Symbol

在 TypeScript 中，`Symbol` 是一种原始数据类型，表示独一无二的值，常用于创建对象属性的唯一标识符。`Symbol` 类型是 ES6 引入的，它可以用来创建独特且不可变的标识符，确保不会发生属性名称的冲突。`Symbol` 是不可变的，也不会被自动转换为其他类型，因此非常适合用于避免对象属性名称的冲突或创建私有成员。

---

## 1. 基本概念

`Symbol` 类型的值是唯一且不可变的，它可以作为对象的键来使用，确保没有其他对象能够拥有相同的键。每次调用 `Symbol()` 方法时，都会返回一个新的独一无二的 `Symbol` 值。

### 创建一个 Symbol

```ts
let sym1 = Symbol();
let sym2 = Symbol();

console.log(sym1 === sym2);  // 输出: false (每个 Symbol 值都是唯一的)
```

### 创建具有描述的 Symbol

`Symbol` 可以传递一个可选的描述参数，这个描述是给 `Symbol` 提供一个可读的标签，便于调试，但它并不会影响 `Symbol` 的唯一性。

```ts
let sym1 = Symbol("description1");
let sym2 = Symbol("description2");

console.log(sym1);  // 输出: Symbol(description1)
console.log(sym2);  // 输出: Symbol(description2)
```

描述主要用于调试和日志输出，在代码中并不会作为标识符的一部分。

---

## 2. 示例

### 2.1 **用于对象的键**

`Symbol` 最常见的应用是作为对象属性的键。因为 `Symbol` 是唯一的，所以它能保证不会与其他属性发生冲突。即使不同的代码或库使用了相同的 `Symbol`，它们也不会冲突。

```ts
let sym = Symbol("key");
let obj = {
  [sym]: "value"
};

console.log(obj[sym]);  // 输出: value
```

由于 `Symbol` 是唯一的，所以不同代码中定义的相同 `Symbol` 键的值不会冲突。

### 2.2 **隐式私有属性**

`Symbol` 也常用于模拟私有属性。通过使用 `Symbol` 作为对象的属性名，可以确保这些属性不会被外部直接访问，从而实现类似私有属性的效果。

```ts
const privateProp = Symbol("private");

class MyClass {
  private [privateProp]: string;

  constructor(value: string) {
    this[privateProp] = value;
  }

  getPrivateValue() {
    return this[privateProp];
  }
}

let obj = new MyClass("Secret");
console.log(obj.getPrivateValue());  // 输出: Secret
console.log(obj.privateProp);        // 编译错误: Property 'privateProp' is private
```

在上述代码中，`privateProp` 是一个 Symbol，用作 `MyClass` 的私有属性，外部无法直接访问。

### 2.3 **避免属性冲突**

当你使用第三方库或与其他开发者的代码合作时，可能会遇到属性名称冲突的情况。使用 `Symbol` 可以有效避免这种冲突，因为每个 `Symbol` 的值都是唯一的。

```ts
let sym1 = Symbol("method");
let sym2 = Symbol("method");

let obj = {
  [sym1]: () => { console.log("Method 1"); },
  [sym2]: () => { console.log("Method 2"); }
};

obj[sym1]();  // 输出: Method 1
obj[sym2]();  // 输出: Method 2
```

### 2.4 **全局 Symbol 注册**

ES6 引入了一个全局的 `Symbol.for()` 方法，它允许你在全局注册一个 `Symbol`，并可以通过 `Symbol.for()` 来获取它。这个方法可以用于跨环境共享 Symbol。

```ts
let sym1 = Symbol.for("sharedKey");
let sym2 = Symbol.for("sharedKey");

console.log(sym1 === sym2);  // 输出: true (Symbol 是全局唯一的)
```

使用 `Symbol.for()` 注册的 Symbol 是全局共享的，这意味着在不同的 JavaScript 执行环境中，它们都能访问到相同的 `Symbol`。

---

## 3. 常用方法

### 3.1 **`Symbol.for()`**

`Symbol.for()` 方法用于查找当前全局环境下注册的 `Symbol`，如果存在，则返回该 `Symbol`，否则会新建一个全局 `Symbol`。

```ts
let sym1 = Symbol.for("mySymbol");
let sym2 = Symbol.for("mySymbol");

console.log(sym1 === sym2);  // 输出: true
```

### 3.2 **`Symbol.keyFor()`**

`Symbol.keyFor()` 方法用于返回全局 `Symbol` 的键（如果该 `Symbol` 是通过 `Symbol.for()` 创建的），如果 `Symbol` 不是全局注册的，返回 `undefined`。

```ts
let sym = Symbol.for("mySymbol");
console.log(Symbol.keyFor(sym));  // 输出: "mySymbol"
```

### 3.3 **`Symbol.iterator`**

`Symbol.iterator` 是一个内置的 `Symbol`，用于定义对象的默认迭代器，允许对象成为可迭代的（例如数组、字符串等）。它通常与 `for...of` 循环配合使用。

```ts
let arr = [1, 2, 3];
let iterator = arr[Symbol.iterator]();

console.log(iterator.next());  // 输出: { value: 1, done: false }
console.log(iterator.next());  // 输出: { value: 2, done: false }
console.log(iterator.next());  // 输出: { value: 3, done: false }
console.log(iterator.next());  // 输出: { value: undefined, done: true }
```

### 3.4 **`Symbol.toStringTag`**

`Symbol.toStringTag` 是另一个内置的 `Symbol`，它允许你定义对象的字符串表示。通常，它用于定制对象在调用 `Object.prototype.toString()` 时返回的字符串。

```ts
let obj = {
  [Symbol.toStringTag]: "MyObject"
};

console.log(Object.prototype.toString.call(obj));  // 输出: [object MyObject]
```


