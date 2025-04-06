# TypeScript基础-元组


在 TypeScript 中，**元组**（Tuple）是一种特殊类型的数组，它允许存储不同类型的元素，并且可以明确指定每个元素的类型。与常规的数组不同，元组的长度和元素的类型是固定的。

## 1. **元组的定义**

元组的定义使用 `[]`，但是每个元素的类型是显式指定的，可以通过逗号分隔的方式定义不同类型的元素。

### 示例：

```ts
let tuple: [string, number, boolean] = ["Hello", 42, true];
```

在这个例子中，`tuple` 是一个元组类型，它的第一个元素是 `string` 类型，第二个元素是 `number` 类型，第三个元素是 `boolean` 类型。

## 2. **元组的特性**

- **长度固定**：元组的长度在声明时就已经确定，不能动态改变。
- **元素类型固定**：元组中的每个元素都有固定的类型，且类型顺序不可更改。
- **支持不同类型**：元组可以包含不同类型的元素，像是字符串、数字、布尔值等。

### 示例：不同类型的元素

```ts
let tuple: [string, number, boolean] = ["TypeScript", 2025, true];
```

## 3. **元组的访问**

元组的元素可以通过索引访问，索引从 `0` 开始。

```ts
let tuple: [string, number] = ["Apple", 5];
console.log(tuple[0]);  // 输出: "Apple"
console.log(tuple[1]);  // 输出: 5
```

## 4. **元组类型的推断**

TypeScript 会根据初始化时的值推断元组的类型。例如：

```ts
let tuple = ["Hello", 100];  // 类型推断为 [string, number]
```

如果不明确指定元组类型，TypeScript 会根据初始化的元素类型来推断元组的类型。

## 5. **元组元素的可选性**

元组的元素可以是可选的（使用 `?`），这允许某些元素可以缺失。

```ts
let tuple: [string, number?] = ["Hello"];  // 第二个元素是可选的
```

在这个例子中，`tuple` 可以是 `["Hello"]` 或 `["Hello", 100]`，第二个元素是可选的。

## 6. **元组的推导与解构**

你可以通过解构赋值来访问元组的各个元素：

```ts
let tuple: [string, number] = ["TypeScript", 2025];
let [language, year] = tuple;

console.log(language);  // 输出: "TypeScript"
console.log(year);      // 输出: 2025
```

## 7. **元组的长度限制**

在元组类型中，长度是固定的。你不能为元组添加超过指定数量的元素：

```ts
let tuple: [string, number] = ["Apple", 5];
tuple.push(3);  // 编译错误: Argument of type 'number' is not assignable to parameter of type 'never'.
```

## 8. **剩余元素元组（Rest Tuple）**

TypeScript 允许在元组中使用剩余元素（`...`），这使得元组可以接受可变数量的元素。剩余元素将会被推断为数组类型。

```ts
let tuple: [string, ...number[]] = ["Age", 21, 22, 23];  // 第一个元素是string，剩余元素是number[]
```

这个例子中，`tuple` 的第一个元素是 `string` 类型，后面的元素是 `number` 类型的数组。

## 9. **元组与数组的区别**

- 元组和数组都使用方括号 `[]` 定义，但元组的元素类型是固定的，数组的元素类型通常是相同的。
- 元组的长度是固定的，而数组的长度可以动态变化。
- 元组通常用来表示具有不同类型的多个值，数组用来表示相同类型的多个值。

### 示例：数组与元组的区别

```ts
// 数组，所有元素必须是 number 类型
let arr: number[] = [1, 2, 3];

// 元组，第一个是 string，第二个是 number
let tuple: [string, number] = ["Hello", 100];
```

