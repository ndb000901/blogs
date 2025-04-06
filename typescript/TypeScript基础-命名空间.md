# TypeScript基础-命名空间

在 TypeScript 中，**命名空间**（`namespace`）是一种将代码组织成模块的机制，它帮助将变量、函数、接口、类等组织在一个单一的作用域中。命名空间通常用于避免全局作用域污染，也有助于提高代码的可维护性和可读性。

命名空间在 TypeScript 中通过 `namespace` 关键字来定义。命名空间内部可以包含多个不同的成员，例如变量、函数、接口、类、类型等。

## 1. **命名空间的定义**

可以使用 `namespace` 关键字来定义一个命名空间，然后将代码内容放入其中。命名空间可以嵌套，也可以跨多个文件进行引用。

### 示例：基本命名空间

```ts
namespace MyNamespace {
    export const pi = 3.14159;
    export function calculateArea(radius: number): number {
        return pi * radius * radius;
    }
}

console.log(MyNamespace.pi);  // 输出: 3.14159
console.log(MyNamespace.calculateArea(10));  // 输出: 314.159
```

在上面的例子中，`MyNamespace` 是一个命名空间，里面包含了一个常量 `pi` 和一个函数 `calculateArea`。由于使用了 `export` 关键字，外部可以访问这些成员。

## 2. **命名空间的成员**

命名空间可以包含多种类型的成员，如常量、函数、接口、类、枚举、类型别名等。所有的成员都会被封装在命名空间内部，外部无法直接访问，除非它们被 `export`。

#### 示例：命名空间内包含不同成员

```ts
namespace Shapes {
    export const circle = "circle";
    export const square = "square";

    export function area(radius: number): number {
        return Math.PI * radius * radius;
    }

    export class Rectangle {
        constructor(public width: number, public height: number) {}
        getArea() {
            return this.width * this.height;
        }
    }
}

console.log(Shapes.circle);  // 输出: "circle"
console.log(Shapes.area(5));  // 输出: 78.53981633974483
const rect = new Shapes.Rectangle(5, 10);
console.log(rect.getArea());  // 输出: 50
```

在这个例子中，`Shapes` 命名空间包含了字符串常量、函数和类。通过 `export` 使得这些成员可以在命名空间外部访问。

## 3. **命名空间的嵌套**

命名空间可以嵌套，这使得可以将相关的功能进一步组织成多个层次。

### 示例：嵌套命名空间

```ts
namespace Shapes {
    export const circle = "circle";

    export namespace Rectangle {
        export const type = "rectangle";
        export function area(width: number, height: number): number {
            return width * height;
        }
    }
}

console.log(Shapes.circle);  // 输出: "circle"
console.log(Shapes.Rectangle.type);  // 输出: "rectangle"
console.log(Shapes.Rectangle.area(5, 10));  // 输出: 50
```

在这个例子中，`Rectangle` 命名空间被嵌套在 `Shapes` 命名空间内。这样可以进一步细分和组织代码。

## 4. **命名空间的跨文件使用**

你可以将命名空间的定义分布在多个文件中，只要确保在最终的应用中包含所有相关的文件。TypeScript 会自动合并这些命名空间，使得它们能够跨文件共享。

### 示例：跨文件使用命名空间

假设有两个文件，`shapes.ts` 和 `main.ts`：

**shapes.ts**：

```ts
namespace Shapes {
    export const circle = "circle";
    export function area(radius: number): number {
        return Math.PI * radius * radius;
    }
}
```

**main.ts**：

```ts
/// <reference path="shapes.ts" />

console.log(Shapes.circle);  // 输出: "circle"
console.log(Shapes.area(10));  // 输出: 314.1592653589793
```

通过在 `main.ts` 中使用 `/// <reference path="shapes.ts" />`，TypeScript 会自动合并 `shapes.ts` 中的命名空间，允许你在 `main.ts` 中访问 `Shapes` 命名空间的成员。

## 5. **命名空间与模块的区别**

在 TypeScript 中，命名空间和模块都用于组织代码，但有以下区别：

- **命名空间**：用于将代码组织在一个全局作用域中，通常用于内部代码的组织。
- **模块**（`module` 或 `import/export`）：使用模块系统，可以将代码拆分成多个文件，并通过 `import` 和 `export` 来共享和引用这些文件中的成员。模块的作用域是独立的。

命名空间在较旧的 TypeScript 版本中非常常见，但在现代 TypeScript 中，推荐使用 ES6 模块（`import`/`export`）来组织代码。尽管如此，命名空间依然可以用于一些特定的场景，尤其是在需要将多个相关功能组织到同一个命名空间中的时候。


