# TypeScript基础-Map


## 1. `Map` 的基本语法

`Map` 的基本语法是通过 `new Map()` 创建一个新的 Map 对象。可以通过一组键值对初始化 `Map`。

### 创建一个 Map 实例

```ts
let map = new Map();  // 创建一个空的 Map
```

### 使用数组初始化 Map

```ts
let map = new Map([
  ["name", "Alice"],
  ["age", 25],
  ["city", "New York"]
]);
```

---

## 2. Map 的操作方法

### 2.1 `set(key, value)`

`set()` 方法用于设置一个键值对。如果 `Map` 中已存在该键，`set()` 会更新其对应的值。

```ts
let map = new Map();
map.set("name", "Alice");
map.set("age", 25);
console.log(map);  // 输出: Map(2) { 'name' => 'Alice', 'age' => 25 }
```

### 2.2 `get(key)`

`get()` 方法用于根据键获取对应的值。如果键不存在，返回 `undefined`。

```ts
let map = new Map([["name", "Alice"]]);
let name = map.get("name");
console.log(name);  // 输出: "Alice"
```

### 2.3 `has(key)`

`has()` 方法用于检查 `Map` 中是否存在某个键。返回一个布尔值。

```ts
let map = new Map([["name", "Alice"], ["age", 25]]);
let hasName = map.has("name");
console.log(hasName);  // 输出: true
```

### 2.4 `delete(key)`

`delete()` 方法用于删除指定键及其对应的值。返回一个布尔值，表示删除是否成功。

```ts
let map = new Map([["name", "Alice"], ["age", 25]]);
map.delete("name");
console.log(map);  // 输出: Map(1) { 'age' => 25 }
```

### 2.5 `clear()`

`clear()` 方法用于删除 `Map` 中的所有键值对。

```ts
let map = new Map([["name", "Alice"], ["age", 25]]);
map.clear();
console.log(map);  // 输出: Map(0) {}
```

### 2.6 `size`

`size` 属性返回 `Map` 中键值对的数量。

```ts
let map = new Map([["name", "Alice"], ["age", 25]]);
console.log(map.size);  // 输出: 2
```

---

## 3. 遍历 Map

`Map` 提供了几种方法来遍历其内容，常见的有 `forEach()` 方法和 `for...of` 循环。

### 1. `forEach()`

`forEach()` 方法对 `Map` 中的每个键值对执行一次回调函数。

```ts
let map = new Map([
  ["name", "Alice"],
  ["age", 25]
]);

map.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});
// 输出:
// name: Alice
// age: 25
```

### 2. `for...of` 循环

`for...of` 可以用来遍历 `Map` 中的键、值或键值对。

#### 遍历键值对

```ts
let map = new Map([
  ["name", "Alice"],
  ["age", 25]
]);

for (let [key, value] of map) {
  console.log(`${key}: ${value}`);
}
// 输出:
// name: Alice
// age: 25
```

#### 遍历键

```ts
let map = new Map([
  ["name", "Alice"],
  ["age", 25]
]);

for (let key of map.keys()) {
  console.log(key);
}
// 输出:
// name
// age
```

#### 遍历值

```ts
let map = new Map([
  ["name", "Alice"],
  ["age", 25]
]);

for (let value of map.values()) {
  console.log(value);
}
// 输出:
// Alice
// 25
```



