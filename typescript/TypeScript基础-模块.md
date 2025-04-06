# TypeScript基础-模块


在 TypeScript 中，**模块**（Modules）是一种将代码组织成更小、更可管理的片段的机制。模块允许你将代码分隔成多个文件，并且通过导出和导入来共享代码。这种机制使得大型应用程序的开发更加简洁、清晰并且易于维护。

## 1. **模块的基本概念**

模块是 TypeScript 或 JavaScript 中的一个文件。每个文件都可以定义其自己的私有作用域，通过 `export` 来暴露自己的一部分功能，其他文件可以通过 `import` 来访问。

- **导出（Export）**：用来指定哪些代码片段可以被外部文件使用。
- **导入（Import）**：用来引入其他模块导出的代码。

## 2. **模块的导出（Export）**

在 TypeScript 中，可以使用 `export` 关键字将变量、函数、类、接口等导出，使其在其他模块中可以被访问。

### 2.1 **命名导出（Named Export）**

通过命名导出，我们可以导出多个对象，并且可以在导入时使用相同的名称来访问。

```ts
// file1.ts
export const foo = 42;
export function greet(name: string) {
  return `Hello, ${name}!`;
}
```

### 2.2 **默认导出（Default Export）**

通过默认导出，一个模块只能有一个默认导出。它通常用于导出单个对象、函数或类。

```ts
// file2.ts
export default function greet(name: string) {
  return `Hello, ${name}!`;
}
```

在这种情况下，你导入时可以使用任意名称来引用默认导出的内容。

```ts
// file3.ts
import greet from './file2';
console.log(greet('World')); // Hello, World!
```

### 2.3 **导出类型和接口**

除了导出函数和变量，我们还可以导出类型和接口。

```ts
// file4.ts
export interface Person {
  name: string;
  age: number;
}

export type NumberOrString = number | string;
```

## 3. **模块的导入（Import）**

导入是通过 `import` 关键字来实现的。你可以导入单个元素，或者导入整个模块。

### 3.1 **命名导入（Named Import）**

导入时需要用相同的名称来匹配模块导出的内容。

```ts
// file5.ts
import { foo, greet } from './file1';

console.log(foo); // 42
console.log(greet('Alice')); // Hello, Alice!
```

### 3.2 **导入整个模块**

你也可以将整个模块导入为一个命名空间，这样就可以通过该命名空间访问所有导出的内容。

```ts
// file6.ts
import * as module1 from './file1';

console.log(module1.foo); // 42
console.log(module1.greet('Bob')); // Hello, Bob!
```

### 3.3 **导入默认导出**

导入默认导出的内容时，你可以使用任意名称来引用。

```ts
// file7.ts
import greet from './file2';

console.log(greet('World')); // Hello, World!
```

### 3.4 **导入类型（Type Only Imports）**

TypeScript 允许仅导入类型而不导入实际的实现代码。这对于避免不必要的运行时负担特别有用。

```ts
// file8.ts
import { Person } from './file4';

const person: Person = {
  name: 'John',
  age: 30,
};
```

在这个例子中，我们只导入了类型 `Person`，它不会在编译后的 JavaScript 中出现。

## 4. **模块解析与路径别名**

TypeScript 支持基于相对路径和绝对路径的模块解析。可以通过 `tsconfig.json` 配置文件来配置模块的解析方式，并为常用的模块路径设置别名。

### 4.1 **配置模块路径**

可以在 `tsconfig.json` 中使用 `baseUrl` 和 `paths` 来设置模块的基本路径和路径别名。

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./src",
    "paths": {
      "@utils/*": ["utils/*"]
    }
  }
}
```

这样，在导入时你可以使用 `@utils` 来引用 `utils` 目录中的模块。

```ts
import { myUtility } from '@utils/myUtility';
```

### 4.2 **模块解析方式**

TypeScript 默认使用相对路径进行模块解析（即从当前文件的位置开始查找），你可以通过修改 `moduleResolution` 设置来选择不同的解析策略（如 `node` 或 `classic`）。

```json
{
  "compilerOptions": {
    "moduleResolution": "node"
  }
}
```

## 5. **模块的类型定义文件**

如果你在使用一个第三方库，并且该库没有 TypeScript 支持，你可以使用类型定义文件来为该库提供类型信息。TypeScript 类型定义文件的扩展名为 `.d.ts`。

### 5.1 **创建类型定义文件**

假设正在使用一个没有类型定义的 JavaScript 库，你可以手动创建一个类型定义文件。例如：

```ts
// myLibrary.d.ts
declare module 'myLibrary' {
  export function someFunction(): void;
}
```

### 5.2 **使用 `@types` 目录**

很多流行的 JavaScript 库有专门的类型定义文件，它们被托管在 `DefinitelyTyped` 仓库中，并可以通过 `npm` 安装。你可以通过以下命令来安装这些类型定义：

```bash

npm install @types/lodash
```

这将自动为 `lodash` 库提供类型支持。

## 6. **模块与命名空间的区别**

在 TypeScript 中，**模块**和**命名空间**都用于组织代码，但它们有一些不同点。

- **模块**：每个模块是一个文件，可以通过 `export` 和 `import` 来导出和导入内容。模块在编译时会被分开，支持外部文件引用。
- **命名空间**：命名空间是将代码组织成一个逻辑单元的方式，不需要文件间的引用。命名空间会被合并在同一个作用域中。

一般来说，现代 TypeScript 推荐使用模块，而非命名空间，因为模块更具灵活性和可扩展性。

