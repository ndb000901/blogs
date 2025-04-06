# TypeScript基础-函数


## 1. 函数的定义

### **基本函数语法**

```ts
function functionName(parameter1: type, parameter2: type): returnType {
  // 函数体
  return result;
}
```

- `functionName`：函数名。
- `parameter1: type, parameter2: type`：参数及其类型注解。
- `: returnType`：返回值的类型注解。
- `return result;`：函数返回的值。

### 示例：

```ts
function add(x: number, y: number): number {
  return x + y;
}

console.log(add(5, 3));  // 8
```

---

## 2. 可选参数与默认参数

### **可选参数**

- 可选参数可以通过 `?` 来标记。它使得参数成为可选的，调用时可以不传递该参数。
- 如果没有传递该参数，参数的值为 `undefined`。

```ts
function greet(name: string, age?: number): string {
  if (age) {
    return `Hello, ${name}. You are ${age} years old.`;
  }
  return `Hello, ${name}.`;
}

console.log(greet("Alice"));              // "Hello, Alice."
console.log(greet("Bob", 25));            // "Hello, Bob. You are 25 years old."
```

### **默认参数**

- 默认参数用于在参数没有传值时赋予一个默认值。

```ts
function greet(name: string, age: number = 30): string {
  return `Hello, ${name}. You are ${age} years old.`;
}

console.log(greet("Alice"));              // "Hello, Alice. You are 30 years old."
console.log(greet("Bob", 25));            // "Hello, Bob. You are 25 years old."
```

---

## 3. 剩余参数（Rest Parameters）

剩余参数（`...`）允许你将多个参数收集为一个数组。它使得函数可以接受不定数量的参数。

```ts
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3));      // 6
console.log(sum(5, 10, 15, 20)); // 50
```

---

## 4. 函数类型

在 TypeScript 中，可以为函数声明指定类型。通过类型别名或接口，可以更加灵活地描述函数类型。

### **类型别名**：

```ts
type GreetFunction = (name: string, age: number) => string;

const greet: GreetFunction = (name, age) => `Hello, ${name}. You are ${age} years old.`;

console.log(greet("Alice", 30));  // "Hello, Alice. You are 30 years old."
```

### **接口**：

```ts
interface Greet {
  (name: string, age: number): string;
}

const greet: Greet = (name, age) => `Hello, ${name}. You are ${age} years old.`;

console.log(greet("Bob", 25));  // "Hello, Bob. You are 25 years old."
```

---

## 5. 函数重载（Function Overloading）

TypeScript 支持函数重载，它允许同一个函数根据不同的参数类型或参数个数有不同的实现。可以在函数声明部分定义多个签名，然后在函数体内进行处理。

### 示例：

```ts
function greet(name: string): string;
function greet(name: string, age: number): string;
function greet(name: string, age?: number): string {
  if (age) {
    return `Hello, ${name}. You are ${age} years old.`;
  }
  return `Hello, ${name}.`;
}

console.log(greet("Alice"));        // "Hello, Alice."
console.log(greet("Bob", 25));      // "Hello, Bob. You are 25 years old."
```

---

## 6. 箭头函数（Arrow Functions）

箭头函数是简洁的函数表达式。它通过 `=>` 语法定义，且不绑定 `this`，使得在某些情况下更加易于使用。

### 基本语法：

```ts
const add = (x: number, y: number): number => x + y;

console.log(add(3, 4));  // 7
```

### 示例（带有 `this`）：

```ts
const obj = {
  value: 5,
  add: function (x: number) {
    return this.value + x;
  },
};

console.log(obj.add(3));  // 8

const arrowObj = {
  value: 5,
  add: (x: number) => this.value + x,  // `this` 在箭头函数中不绑定
};

console.log(arrowObj.add(3));  // NaN，因为 `this` 是全局对象
```

---

## 7. 内联函数表达式与立即调用函数表达式（IIFE）

### **内联函数表达式（Anonymous Function Expression）**

匿名函数是没有名字的函数，常用于事件处理、回调等场合。

```ts
setTimeout(function() {
  console.log("This is an anonymous function");
}, 1000);
```

### **立即调用函数表达式（IIFE）**

IIFE 是一种函数立即执行的方式。它被包裹在圆括号中并立即调用。

```ts
(function() {
  console.log("This function runs immediately!");
})();
```

---

## 8. 高阶函数

高阶函数是指接受函数作为参数或返回值是函数的函数。这使得函数更具灵活性。

### 示例：

```ts
// 接受一个函数作为参数
function repeat(n: number, action: () => void): void {
  for (let i = 0; i < n; i++) {
    action();
  }
}

repeat(3, () => console.log("Hello World!"));
// 输出：Hello World!
// 输出：Hello World!
// 输出：Hello World!
```

---

## 9. 函数作用域与闭包

- **作用域（Scope）**：函数内部声明的变量在函数外部不可访问。
- **闭包（Closure）**：一个函数能够“记住”并访问其词法作用域，即使在其外部执行。

### 示例：

```ts
function outer() {
  let count = 0;
  return function inner() {
    count++;
    console.log(count);
  };
}

const counter = outer();
counter();  // 1
counter();  // 2
counter();  // 3
```

---



