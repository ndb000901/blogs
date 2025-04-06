# TypeScript基础-Set

在 TypeScript 中，`Set` 是一种集合类型的数据结构，用于存储唯一的值。

## 1. `Set` 的基本语法

`Set` 的基本语法是通过 `new Set()` 创建一个新的 `Set` 对象。可以通过数组初始化 `Set`，也可以创建空 `Set`。

### 创建一个 `Set` 实例

```ts
let set = new Set();  // 创建一个空的 Set
```

### 使用数组初始化 `Set`

```ts
let set = new Set([1, 2, 3, 4]);
console.log(set);  // 输出: Set(4) { 1, 2, 3, 4 }
```

---

## 2. `Set` 的操作方法

### 2.1 `add(value)`

`add()` 方法用于向 `Set` 中添加一个新的元素。如果元素已经存在，`Set` 会忽略该元素。

```ts
let set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(1);  // 1 已经存在，忽略
console.log(set);  // 输出: Set(3) { 1, 2, 3 }
```

### 2.2 `has(value)`

`has()` 方法用于检查 `Set` 中是否存在某个元素。返回一个布尔值。

```ts
let set = new Set([1, 2, 3]);
let hasTwo = set.has(2);
console.log(hasTwo);  // 输出: true
```

### 2.3 `delete(value)`

`delete()` 方法用于删除 `Set` 中指定的元素。如果该元素存在，返回 `true`，否则返回 `false`。

```ts
let set = new Set([1, 2, 3]);
set.delete(2);
console.log(set);  // 输出: Set(2) { 1, 3 }
```

### 2.4 `clear()`

`clear()` 方法用于删除 `Set` 中的所有元素。

```ts
let set = new Set([1, 2, 3]);
set.clear();
console.log(set);  // 输出: Set(0) {}
```

### 2.5 `size`

`size` 属性返回 `Set` 中元素的数量。

```ts
let set = new Set([1, 2, 3]);
console.log(set.size);  // 输出: 3
```

---

## 3. 遍历 `Set`

`Set` 提供了几种方法来遍历其内容，常见的有 `forEach()` 方法和 `for...of` 循环。

### 3.1 `forEach()`

`forEach()` 方法对 `Set` 中的每个元素执行一次回调函数。

```ts
let set = new Set([1, 2, 3]);

set.forEach(value => {
  console.log(value);
});
// 输出:
// 1
// 2
// 3
```

### 3.2 `for...of` 循环

`for...of` 可以用来遍历 `Set` 中的元素。

```ts
let set = new Set([1, 2, 3]);

for (let value of set) {
  console.log(value);
}
// 输出:
// 1
// 2
// 3
```

