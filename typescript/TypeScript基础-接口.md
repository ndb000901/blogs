# TypeScript基础-接口


## 1. 接口的基本语法

接口的基本语法使用 `interface` 关键字来定义，后面跟着一个接口的名称和花括号 `{}` 内的属性或方法定义。

```ts
interface InterfaceName {
  property: type;
  method(): returnType;
}
```

### 示例：简单接口

```ts
interface Person {
  name: string;
  age: number;
}

const person: Person = {
  name: "John",
  age: 30,
};
```

---

## 2. 接口的属性

接口可以定义多个属性，并且每个属性都可以有不同的类型。

```ts
interface Car {
  brand: string;
  model: string;
  year: number;
}

const myCar: Car = {
  brand: "Toyota",
  model: "Corolla",
  year: 2021,
};
```

### 可选属性

通过 `?` 可以使接口的某个属性变成可选的。

```ts
interface Person {
  name: string;
  age?: number; // 可选属性
}

const person1: Person = { name: "John" };
const person2: Person = { name: "Alice", age: 25 };
```

### 只读属性

通过 `readonly` 修饰符，可以使属性成为只读属性，防止被修改。

```ts
interface Point {
  readonly x: number;
  readonly y: number;
}

const point: Point = { x: 10, y: 20 };
// point.x = 15; // 错误，x 是只读的
```

---

## 3. 接口的方法

接口不仅可以定义属性，还可以定义方法。这些方法可以像普通函数一样具有参数和返回类型。

```ts
interface Greetable {
  greet(): void;
}

class Person implements Greetable {
  greet() {
    console.log("Hello!");
  }
}

const person = new Person();
person.greet(); // 输出 "Hello!"
```

### 方法的参数和返回类型

接口可以定义方法的参数类型和返回类型。

```ts
interface MathOperations {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
}

const math: MathOperations = {
  add(a, b) {
    return a + b;
  },
  subtract(a, b) {
    return a - b;
  },
};

console.log(math.add(5, 3));    // 8
console.log(math.subtract(5, 3)); // 2
```

---

## 4. 接口的继承

接口可以通过继承其他接口来扩展其属性和方法。继承后的接口可以添加新的属性或方法，也可以覆盖原接口的属性和方法。

### 接口继承

```ts
interface Animal {
  name: string;
}

interface Dog extends Animal {
  bark(): void;
}

const dog: Dog = {
  name: "Buddy",
  bark() {
    console.log("Woof!");
  },
};

dog.bark(); // 输出 "Woof!"
```

### 继承多个接口

接口可以继承多个接口，组合它们的属性和方法。

```ts
interface Animal {
  name: string;
}

interface Mammal {
  hasFur: boolean;
}

interface Dog extends Animal, Mammal {
  bark(): void;
}

const dog: Dog = {
  name: "Buddy",
  hasFur: true,
  bark() {
    console.log("Woof!");
  },
};
```

---

## 5. 类实现接口

TypeScript 中的类可以使用 `implements` 关键字来实现接口，确保类中的属性和方法符合接口的定义。

```ts
interface Greetable {
  greet(): void;
}

class Person implements Greetable {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, ${this.name}!`);
  }
}

const person = new Person("Alice");
person.greet(); // 输出 "Hello, Alice!"
```

---

## 6. 接口与函数类型

接口也可以用来定义函数类型。你可以在接口中描述一个函数签名，即该函数的参数和返回类型。

```ts
interface AddFunction {
  (a: number, b: number): number;
}

const add: AddFunction = (a, b) => a + b;

console.log(add(2, 3)); // 5
```

---

## 7. 接口与索引签名

接口可以定义索引签名，用来描述动态属性。通过索引签名，接口可以表示一个对象具有任意数量的属性，每个属性的键和值的类型都是已知的。

```ts
interface StringDictionary {
  [key: string]: string;
}

const dict: StringDictionary = {
  firstName: "John",
  lastName: "Doe",
};

console.log(dict.firstName); // 输出 "John"
```

---

## 8. 接口的合并

在 TypeScript 中，如果你定义了多个同名的接口，它们会自动合并。这样，你可以向同一个接口添加更多的属性。

```ts
interface Person {
  name: string;
}

interface Person {
  age: number;
}

const person: Person = {
  name: "John",
  age: 30,
};
```

---

## 9. 泛型接口

接口也可以与泛型结合使用，允许接口的属性或方法接受类型参数。

```ts
interface Box<T> {
  value: T;
}

const box: Box<number> = { value: 42 };
const stringBox: Box<string> = { value: "Hello" };
```

### 泛型接口的方法

```ts
interface Identity<T> {
  (value: T): T;
}

const identity: Identity<number> = (value) => value;

console.log(identity(5)); // 输出 5
```


