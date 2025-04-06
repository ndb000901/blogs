# TypeScript基础-装饰器

在 TypeScript 中，**装饰器（Decorators）** 是一种特殊的声明，用于对类、方法、访问器、属性或参数进行注解或修改。装饰器本质上是一个函数，它可以被附加到类或其成员上，从而动态地修改类的行为或添加元数据。

装饰器通常用于以下目的：

- **注解类或方法**：提供额外的元数据或功能。
- **修改类或方法的行为**：例如，修改方法的执行逻辑，或者对类进行增强。

## 1. **启用装饰器**

要在 TypeScript 中使用装饰器，需要在 `tsconfig.json` 文件中启用 `experimentalDecorators` 选项。

```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

## 2. **装饰器的类型**

装饰器的类型与它们所装饰的目标类型相关。TypeScript 支持几种不同类型的装饰器：

- **类装饰器（Class Decorators）**
- **方法装饰器（Method Decorators）**
- **属性装饰器（Property Decorators）**
- **访问器装饰器（Accessor Decorators）**
- **参数装饰器（Parameter Decorators）**

每种装饰器的使用方式和定义都有一定的差异。

## 3. **类装饰器（Class Decorators）**

类装饰器用于装饰类构造函数。它们接收一个类构造函数作为参数，并且可以对类进行修改或替换。

#### 示例：类装饰器

```ts
function logClass(target: Function) {
  console.log(`Class ${target.name} is created`);
}

@logClass
class MyClass {
  constructor() {
    console.log("MyClass instance created");
  }
}

const obj = new MyClass();  // 输出：Class MyClass is created
                           //        MyClass instance created
```

在这个例子中，`logClass` 是一个类装饰器，它接受类构造函数 `target` 作为参数。装饰器会在类创建时执行，在控制台输出类的名称。

## 4. **方法装饰器（Method Decorators）**

方法装饰器用于装饰类中的方法。它们接收三个参数：

- **target**：类的原型对象（实例方法）或构造函数（静态方法）。
- **propertyKey**：方法的名称。
- **descriptor**：方法的属性描述符（用于修改方法行为）。

### 示例：方法装饰器

```ts
function logMethod(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  descriptor.value = function (...args: any[]) {
    console.log(`Method ${propertyKey} called with args: ${args}`);
    return originalMethod.apply(this, args);
  };
}

class MyClass {
  @logMethod
  greet(name: string) {
    console.log(`Hello, ${name}`);
  }
}

const obj = new MyClass();
obj.greet("Alice");  // 输出：Method greet called with args: [ 'Alice' ]
                     //         Hello, Alice
```

在这个例子中，`logMethod` 装饰器会包装原始的 `greet` 方法，添加日志输出每次方法调用时传入的参数。

## 5. **属性装饰器（Property Decorators）**

属性装饰器用于装饰类中的属性。它们接受两个参数：

- **target**：类的原型对象。
- **propertyKey**：属性的名称。

属性装饰器不能修改属性的值或行为，但是可以用来添加元数据。

### 示例：属性装饰器

```ts
function logProperty(target: any, propertyKey: string) {
  console.log(`Property ${propertyKey} is decorated`);
}

class MyClass {
  @logProperty
  name: string;
  
  constructor(name: string) {
      this.name = name;
  }
}

const obj = new MyClass("hello");  // 输出：Property name is decorated
```

这里，`logProperty` 装饰器只是简单地输出属性的名称。当类的实例化时，装饰器会在控制台打印相应的信息。

## 6. **访问器装饰器（Accessor Decorators）**

访问器装饰器用于装饰类的 getter 和 setter 方法。它们接受与方法装饰器相同的三个参数：`target`、`propertyKey` 和 `descriptor`。

### 示例：访问器装饰器

```ts
function logAccessor(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalGet = descriptor.get;

  descriptor.get = function () {
    console.log(`Getting value of ${propertyKey}`);
    return originalGet?.apply(this);
  };
}

class MyClass {
  private _name: string = "";

  @logAccessor
  get name() {
    return this._name;
  }

  set name(value: string) {
    this._name = value;
  }
}

const obj = new MyClass();
obj.name = "Alice";  // 设置值时不会输出
console.log(obj.name);  // 输出：Getting value of name
                       //         Alice
```

在这个例子中，`logAccessor` 装饰器为 getter 方法添加了日志输出，每次访问 `name` 属性时，都会先输出日志。

## 7. **参数装饰器（Parameter Decorators）**

参数装饰器用于装饰方法中的参数。它们接收三个参数：

- **target**：类的原型对象。
- **propertyKey**：方法的名称。
- **parameterIndex**：参数的索引位置。

### 示例：参数装饰器

```ts
function logParameter(target: any, propertyKey: string, parameterIndex: number) {
  console.log(`Parameter at index ${parameterIndex} in method ${propertyKey} is decorated`);
}

class MyClass {
  greet(@logParameter name: string) {
    console.log(`Hello, ${name}`);
  }
}

const obj = new MyClass();
obj.greet("Alice");  // 输出：Parameter at index 0 in method greet is decorated
                     //         Hello, Alice
```

这里，`logParameter` 装饰器记录每个被装饰的参数的索引和方法名称。

## 8. **组合装饰器**

TypeScript 允许为同一个类或方法应用多个装饰器，这时这些装饰器将按顺序执行。因此，可以结合使用多个装饰器来修改类或方法的行为。

```ts
function decoratorA(target: any) {
  console.log("decoratorA");
}

function decoratorB(target: any) {
  console.log("decoratorB");
}

@decoratorA
@decoratorB
class MyClass {}

const obj = new MyClass();  // 输出：decoratorB
                            //         decoratorA
```

## 9. **装饰器与元编程**

TypeScript 装饰器支持与元编程结合使用，可以在类、方法、属性或参数上添加额外的元数据。例如，使用装饰器记录日志，或者用于依赖注入框架的设计。


