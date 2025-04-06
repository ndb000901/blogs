# TypeScript基础-泛型

在 TypeScript 中，**泛型**（Generics）是一种强大的工具，它允许你在定义函数、类、接口等时指定类型参数。使用泛型可以让你在保持类型安全的同时，使代码更加灵活和可复用。泛型的主要目的是为了在不牺牲类型安全的前提下，能够适应不同的数据类型。

## 1. **泛型的基本概念**

泛型的核心思想是**延迟指定类型**，允许在使用时传入具体的类型参数。它使得你可以编写与类型无关的代码，同时又能保持类型的安全性。

### 示例：使用泛型函数

```ts
// 不使用泛型的函数
function identity(arg: number): number {
  return arg;
}

// 使用泛型的函数
function identity<T>(arg: T): T {
  return arg;
}
```

在上面的例子中，`identity` 函数使用了泛型 `T`，它可以接受任何类型的参数，函数的返回值类型与输入参数类型相同。

## 2. **泛型的语法**

泛型通过在函数、类、接口等后面加上尖括号（`< >`）来定义类型参数。以下是泛型的基本语法：

- **泛型函数**：`function <T>(arg: T): T { return arg; }`
- **泛型接口**：`interface Identity<T> { value: T; }`
- **泛型类**：`class Box<T> { value: T; }`

### 示例：泛型函数

```ts
function getLength<T>(arr: T[]): number {
  return arr.length;
}

console.log(getLength([1, 2, 3])); // 3
console.log(getLength(['a', 'b', 'c'])); // 3
```

这里，`getLength` 函数接受一个泛型数组，并返回其长度。`T[]` 表示 `T` 类型的数组，`T` 是泛型参数。

### 示例：泛型接口

```ts
interface Box<T> {
  value: T;
}

let numberBox: Box<number> = { value: 100 };
let stringBox: Box<string> = { value: 'Hello' };
```

在这个例子中，`Box` 接口接受一个泛型参数 `T`，并且它的 `value` 属性的类型是 `T`。我们可以通过传入不同的类型参数（例如 `number` 或 `string`）来创建不同类型的 `Box`。

## 3. **多种类型的泛型**

一个泛型类型参数可以接受多个类型参数。你可以通过逗号分隔多个类型参数。

### 示例：多个泛型参数

```ts
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

let result = pair(1, 'hello');
console.log(result); // [1, 'hello']
```

在这个例子中，`pair` 函数有两个泛型参数 `T` 和 `U`，它们分别代表两个参数的类型。函数返回一个元组 `[T, U]`，即包含这两个类型的元组。

## 4. **泛型约束**

有时你可能希望对泛型类型进行约束，以限制它必须是某种类型或具备某些特性。TypeScript 提供了 `extends` 关键字来为泛型指定约束。

### 示例：泛型约束

```ts
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): void {
  console.log(arg.length);
}

logLength([1, 2, 3]); // [1, 2, 3]
logLength('Hello, world!'); // 'Hello, world!'
```

在这个例子中，`T extends Lengthwise` 表示泛型参数 `T` 必须是具有 `length` 属性的类型。`logLength` 函数只能接受那些具有 `length` 属性的类型，例如数组、字符串等。

## 5. **默认类型**

泛型可以定义默认类型，若在调用时没有传入具体的类型，默认类型会自动使用。

### 示例：默认类型

```ts
function createArray<T = number>(length: number, value: T): T[] {
  return new Array(length).fill(value);
}

let arr1 = createArray(3, 5); // 数字类型
let arr2 = createArray(3, 'hello'); // 字符串类型
```

在这个例子中，`createArray` 函数的泛型参数 `T` 默认是 `number`，如果在调用时没有提供具体类型参数，那么它会使用 `number` 作为默认类型。

## 6. **泛型类**

类也可以使用泛型，以便在类的内部使用类型参数。泛型类和泛型接口类似，定义时需要在类名后加上泛型参数。

### 示例：泛型类

```ts
class Box<T> {
  private value: T;
  constructor(value: T) {
    this.value = value;
  }
  getValue(): T {
    return this.value;
  }
}

let numberBox = new Box<number>(123);
let stringBox = new Box<string>('Hello');

console.log(numberBox.getValue()); // 123
console.log(stringBox.getValue()); // 'Hello'
```

在这个例子中，`Box` 类是一个泛型类，它接受一个类型参数 `T`，并且它有一个 `value` 属性和一个 `getValue` 方法，返回类型为 `T`。


## 7. **泛型的高级用法**

### 7.1. **条件类型**

泛型类型可以与条件类型结合使用，以根据传入的类型条件来选择不同的类型。

```ts
type IsString<T> = T extends string ? 'Yes' : 'No';

type A = IsString<string>; // 'Yes'
type B = IsString<number>; // 'No'
```

### 7.2. **映射类型**

泛型还可以与映射类型结合，生成一个新类型，它的每个属性都基于另一个类型的属性。

```ts
type ReadOnly<T> = {
  readonly [K in keyof T]: T[K];
};

interface Person {
  name: string;
  age: number;
}

const p: ReadOnly<Person> = { name: 'Alice', age: 30 };
// p.name = 'Bob';  // 错误，属性是只读的
```
