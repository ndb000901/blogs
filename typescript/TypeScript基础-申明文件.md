# TypeScript基础-申明文件

在 TypeScript 中，**声明文件**（Declaration Files）是用来描述现有 JavaScript 代码的类型信息的文件。声明文件通常以 `.d.ts` 扩展名结尾，并且不包含任何实际的实现代码，只是描述类型、接口、类等的结构。这些文件可以帮助 TypeScript 确定在 JavaScript 代码中使用的外部库或模块的类型，增强代码的类型安全性和开发体验。

## 1. **声明文件的作用**

声明文件的主要作用是为 TypeScript 提供类型信息，尤其是在使用 JavaScript 库、第三方模块或现有代码时，它可以让 TypeScript 知道外部库或模块中存在的类型，进而提供类型检查和智能提示。

- **类型安全**：声明文件帮助 TypeScript 知道外部代码的类型，避免因类型不匹配导致的错误。
- **自动补全**：通过提供类型信息，IDE（如 VSCode）可以提供更准确的代码补全。
- **增强开发体验**：通过明确的类型声明，开发者能更清楚地理解和使用外部库的 API。

## 2. **声明文件的基本结构**

声明文件通常包含以下内容：

- **变量声明**：描述全局变量或模块变量的类型。
- **函数声明**：描述函数的签名、参数类型、返回值类型等。
- **接口声明**：描述对象的结构和类型。
- **类声明**：描述类的构造函数、属性和方法。
- **模块声明**：描述第三方模块的类型信息。

### 示例：声明文件 `myLibrary.d.ts`

```ts
// 这是一个声明文件，定义了一个简单的外部库 myLibrary 的类型信息

// 声明一个全局变量
declare const myGlobalVar: string;

// 声明一个函数
declare function myFunction(a: number, b: number): string;

// 声明一个接口
declare interface MyObject {
    name: string;
    age: number;
}

// 声明一个类
declare class MyClass {
    constructor(name: string);
    sayHello(): string;
}

// 声明一个模块
declare module "myModule" {
    export function doSomething(): void;
    export const version: string;
}
```

## 3. **声明文件的常见用法**

### 3.1. **为全局变量或函数提供类型**

在没有模块化的情况下，有些 JavaScript 库可能会将变量或函数直接挂载到全局作用域中。通过声明文件，你可以为这些全局变量和函数提供类型信息。

#### 示例：声明全局变量

```ts
// global.d.ts
declare const GLOBAL_VAR: string;
declare function globalFunction(param: number): void;
```

### 3.2. **为第三方 JavaScript 库提供类型声明**

对于 JavaScript 库，声明文件通常提供库的接口、函数和类型，帮助 TypeScript 用户使用这些库时进行类型检查。

例如，可能使用了一个没有 TypeScript 类型的 JavaScript 库，可以为它编写一个简单的声明文件：

```ts
// thirdPartyLib.d.ts
declare module "third-party-lib" {
    export function doSomething(arg: string): void;
    export const version: string;
}
```

然后你可以在 TypeScript 中安全地使用这个库：

```ts
import { doSomething, version } from "third-party-lib";
doSomething("test");
console.log(version);
```

### 3.3. **模块声明**

对于 Node.js 模块或者 ES 模块，可以通过声明文件为这些模块提供类型信息。模块声明文件通常会描述模块的导出接口和类型。

#### 示例：模块声明

```ts
// myModule.d.ts
declare module "myModule" {
    export function greet(name: string): string;
    export const author: string;
}
```

然后你可以使用该模块，如下所示：

```ts
import { greet, author } from "myModule";

console.log(greet("Alice"));
console.log(author);
```

### 3.4. **声明命名空间**

在没有模块化的情况下，许多 JavaScript 库可能会将功能暴露在命名空间中。你可以通过声明文件为这些命名空间提供类型信息。

#### 示例：命名空间声明

```ts
// utils.d.ts
declare namespace Utils {
    function sum(a: number, b: number): number;
    function multiply(a: number, b: number): number;
}
```

然后你可以在 TypeScript 中访问这些功能：

```ts
console.log(Utils.sum(1, 2));
console.log(Utils.multiply(2, 3));
```

## 4. **声明文件的文件结构**

一个 TypeScript 项目通常将声明文件放在一个专门的目录中，例如 `@types`，或者将声明文件直接放在 `src` 目录中。一个常见的结构如下：

```
/project-root
  /node_modules
  /src
    - main.ts
    - helper.ts
  /types
    - third-party-lib.d.ts
  - tsconfig.json
```

## 5. **使用声明文件**

当 TypeScript 项目需要引入声明文件时，通常有两种方式来引用声明文件：

1. **自动查找**：如果声明文件位于项目中的 `@types` 目录或 `node_modules/@types` 中，TypeScript 会自动识别这些声明文件，无需显式引用。
2. **显式引用**：使用 `/// <reference path="..." />` 来显式地引用声明文件。

### 示例：显式引用声明文件

```ts
/// <reference path="myLibrary.d.ts" />

const myValue: MyObject = { name: "Alice", age: 30 };
console.log(myValue.name);
```

## 6. **`declare` 关键字的使用**

在声明文件中，所有的声明通常都会使用 `declare` 关键字，这意味着这些声明只是类型信息，不会有实际的运行时代码。

- **变量声明**：`declare const` 或 `declare var` 用于声明全局变量。
- **函数声明**：`declare function` 用于声明全局函数。
- **模块声明**：`declare module` 用于声明模块。
- **命名空间声明**：`declare namespace` 用于声明命名空间。

## 7. **常见的声明文件**

- **DefinitelyTyped**：TypeScript 官方社区维护了一个大型开源项目 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)，其中包含了大量流行 JavaScript 库的声明文件。你可以通过 `npm install @types/your-library` 来安装它们。
- **`@types` 包**：很多流行的 JavaScript 库和框架都提供了 `@types` 类型声明包，它们可以直接通过 npm 安装，并且被 TypeScript 自动识别。

