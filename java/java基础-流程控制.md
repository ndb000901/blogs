Java 的流程控制语句用于控制程序的执行路径，常见的流程控制语句包括 **条件语句**、**循环语句** 和 **跳转语句**。这些语句帮助实现不同的逻辑判断、重复执行和跳转功能。

### 1. **条件语句**（Conditional Statements）
条件语句根据不同的条件执行不同的代码块。Java 提供了以下几种条件语句：

#### `if` 语句
用于判断条件是否为真。如果条件为真，执行代码块。

```java
if (condition) {
    // 条件为真时执行的代码
}
```

#### `if-else` 语句
如果条件为真，执行 `if` 代码块，否则执行 `else` 代码块。

```java
if (condition) {
    // 条件为真时执行的代码
} else {
    // 条件为假时执行的代码
}
```

#### `if-else if-else` 语句
用于多个条件判断，依次检查每个条件。

```java
if (condition1) {
    // 条件1为真时执行的代码
} else if (condition2) {
    // 条件2为真时执行的代码
} else {
    // 上述条件都不满足时执行的代码
}
```

#### `switch` 语句
根据变量的值选择执行不同的代码块。`switch` 语句可以用于多个条件判断。

```java
switch (variable) {
    case value1:
        // 变量等于 value1 时执行的代码
        break;
    case value2:
        // 变量等于 value2 时执行的代码
        break;
    default:
        // 变量不匹配任何 case 时执行的代码
}
```

### 2. **循环语句**（Looping Statements）
循环语句用于反复执行某段代码，直到条件不再满足。

#### `for` 循环
最常用的循环语句，适用于已知循环次数的情况。

```java
for (initialization; condition; increment) {
    // 循环体
}
```

示例：打印 1 到 5 的数字：
```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

#### `while` 循环
先判断条件，如果条件为真，则执行循环体。

```java
while (condition) {
    // 循环体
}
```

示例：打印 1 到 5 的数字：
```java
int i = 1;
while (i <= 5) {
    System.out.println(i);
    i++;
}
```

#### `do-while` 循环
先执行一次循环体，然后再判断条件。

```java
do {
    // 循环体
} while (condition);
```

示例：打印 1 到 5 的数字：
```java
int i = 1;
do {
    System.out.println(i);
    i++;
} while (i <= 5);
```

### 3. **跳转语句**（Jump Statements）
跳转语句用于跳转到代码的其他部分。

#### `break` 语句
用于跳出当前循环或 `switch` 语句，终止循环或 `switch` 的执行。

```java
while (true) {
    if (condition) {
        break;  // 跳出循环
    }
}
```

#### `continue` 语句
用于跳过当前循环的剩余部分，直接进入下一次循环判断。

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue;  // 跳过 i == 3 时的代码，进入下一次循环
    }
    System.out.println(i);
}
```

#### `return` 语句
用于从方法中返回，可以返回一个值（如果方法有返回值）。

```java
public int sum(int a, int b) {
    return a + b;  // 返回函数的结果
}
```

#### `throw` 语句
用于显式地抛出异常。

```java
throw new Exception("Some error occurred");
```

#### `throws` 语句
用于声明方法可能会抛出异常，供调用者处理。

```java
public void myMethod() throws IOException {
    // 可能抛出异常的代码
}
```

### 4. **标签与多层循环控制**
`break` 和 `continue` 可以通过标签控制多层循环的执行流。

#### 标签 `break`
跳出多层嵌套循环时，可以使用标签来指定跳出的目标循环。

```java
outerLoop:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) {
            break outerLoop;  // 跳出外层循环
        }
        System.out.println("i=" + i + ", j=" + j);
    }
}
```

#### 标签 `continue`
跳过多层嵌套循环中的某一层时，可以使用标签来指定跳过的目标循环。

```java
outerLoop:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) {
            continue outerLoop;  // 跳过外层循环中的一次迭代
        }
        System.out.println("i=" + i + ", j=" + j);
    }
}
```

### 示例代码：
```java
public class ControlFlowExample {
    public static void main(String[] args) {
        // if-else 示例
        int a = 10;
        if (a > 5) {
            System.out.println("a is greater than 5");
        } else {
            System.out.println("a is less than or equal to 5");
        }

        // switch 示例
        int day = 3;
        switch (day) {
            case 1:
                System.out.println("Monday");
                break;
            case 2:
                System.out.println("Tuesday");
                break;
            case 3:
                System.out.println("Wednesday");
                break;
            default:
                System.out.println("Invalid day");
        }

        // for 循环 示例
        for (int i = 1; i <= 3; i++) {
            System.out.println("i = " + i);
        }

        // while 循环 示例
        int i = 1;
        while (i <= 3) {
            System.out.println("i = " + i);
            i++;
        }

        // do-while 示例
        int j = 1;
        do {
            System.out.println("j = " + j);
            j++;
        } while (j <= 3);

        // break 示例
        for (int k = 1; k <= 5; k++) {
            if (k == 3) {
                break;  // 退出循环
            }
            System.out.println("k = " + k);
        }

        // continue 示例
        for (int l = 1; l <= 5; l++) {
            if (l == 3) {
                continue;  // 跳过当前循环，进入下一次循环
            }
            System.out.println("l = " + l);
        }
    }
}
```
