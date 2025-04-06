# TypeScript基础-类


## 1. 类的基本语法

类的定义通过 `class` 关键字来进行，类的主体包含构造函数、属性、方法等。

### 基本语法

```ts
class ClassName {
  property: type;
  constructor(parameters) {
    // 初始化属性
  }
  method() {
    // 定义方法
  }
}
```

### 示例：简单类

```ts
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log(`Hello, my name is ${this.name} and I am ${this.age} years old.`);
  }
}

const person = new Person("Alice", 25);
person.greet();  // 输出: Hello, my name is Alice and I am 25 years old.
```

---

## 2. 类的属性

类可以有属性，属性用于存储对象的状态或数据。属性可以是公开的、私有的或者受保护的。

### 公共属性（默认）

类的属性默认是**公开的**，意味着可以通过类的实例直接访问。

```ts
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

const person = new Person("Alice", 25);
console.log(person.name);  // 输出 "Alice"
```

### 私有属性

通过 `private` 关键字，可以定义私有属性，这样这些属性只能在类内部访问。

```ts
class Person {
  private name: string;
  private age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  getName() {
    return this.name;
  }
}

const person = new Person("Alice", 25);
// console.log(person.name);  // 错误，'name' 是私有的
console.log(person.getName());  // 输出 "Alice"
```

### 受保护属性

通过 `protected` 关键字，可以定义受保护的属性，这些属性只能在类及其子类中访问。

```ts
class Person {
  protected name: string;

  constructor(name: string) {
    this.name = name;
  }
}

class Employee extends Person {
  constructor(name: string) {
    super(name);
  }

  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}

const employee = new Employee("Bob");
employee.greet();  // 输出 "Hello, my name is Bob."
```

---

## 3. 类的构造函数

构造函数（`constructor`）用于在创建类的实例时初始化对象的属性。构造函数在对象创建时自动调用。

```ts
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

const person = new Person("Alice", 25); // 构造函数被调用
```

---

## 4. 类的方法

类可以包含方法，方法是类中定义的函数，用来执行操作或处理对象的逻辑。

```ts
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}

const person = new Person("Alice");
person.greet();  // 输出 "Hello, my name is Alice."
```

### 方法的访问修饰符

- **public**：默认的访问修饰符，方法可以被类的实例调用。
- **private**：方法只能在类内部访问。
- **protected**：方法可以在类及其子类中访问。

```ts
class Person {
  private greet() {
    console.log("Hello!");
  }

  callGreet() {
    this.greet(); // 可以在类内部调用私有方法
  }
}

const person = new Person();
// person.greet(); // 错误，'greet' 是私有的
person.callGreet(); // 输出 "Hello!"
```

---

## 5. 类的继承

类可以通过 `extends` 关键字继承其他类。继承使得子类可以复用父类的属性和方法，并且可以在子类中扩展或覆盖父类的行为。

```ts
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a sound.`);
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name);  // 调用父类的构造函数
  }

  speak() {
    console.log(`${this.name} barks.`);
  }
}

const dog = new Dog("Buddy");
dog.speak();  // 输出 "Buddy barks."
```

### 调用父类构造函数

子类构造函数中需要使用 `super()` 调用父类的构造函数来初始化继承的属性。

```ts
class Dog extends Animal {
  constructor(name: string, breed: string) {
    super(name);  // 调用父类的构造函数
    this.breed = breed;
  }
}
```

---

## 6. 类的静态成员

类的静态成员是属于类本身而不是实例的。可以通过类名直接访问静态成员。

```ts
class Person {
  static species = "Homo sapiens";  // 静态属性

  constructor(public name: string) {}

  static greet() {  // 静态方法
    console.log("Hello!");
  }
}

console.log(Person.species);  // 输出 "Homo sapiens"
Person.greet();  // 输出 "Hello!"
```

静态成员不能通过实例访问。

```ts
const person = new Person("Alice");
// console.log(person.species); // 错误，'species' 是静态的
```

---

## 7. 抽象类

抽象类是不能被实例化的类。它可以定义抽象方法，这些方法必须在子类中实现。

```ts
abstract class Animal {
  abstract makeSound(): void;  // 抽象方法

  move() {
    console.log("Moving...");
  }
}

class Dog extends Animal {
  makeSound() {
    console.log("Bark!");
  }
}

const dog = new Dog();
dog.makeSound();  // 输出 "Bark!"
```

---

## 8. 类的 getter 和 setter

TypeScript 支持 getter 和 setter，允许你以对象属性的方式访问或修改类的私有属性。

### 示例：getter 和 setter

```ts
class Person {
  private _age: number;

  constructor(age: number) {
    this._age = age;
  }

  get age() {
    return this._age;
  }

  set age(value: number) {
    if (value >= 0) {
      this._age = value;
    } else {
      console.log("Age cannot be negative.");
    }
  }
}

const person = new Person(25);
console.log(person.age);  // 使用 getter 输出 25
person.age = 30;          // 使用 setter 设置 age
console.log(person.age);  // 输出 30
person.age = -5;          // 输出 "Age cannot be negative."
```

---

