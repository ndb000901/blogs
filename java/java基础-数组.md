# java基础-数组

Java 中的数组是一种容器，用于存储多个相同类型的数据元素。数组在 Java 中是一种引用类型，可以存储基本数据类型（如 `int`、`char` 等）或对象引用。数组的大小在创建时固定，并且一旦定义，就不能改变。

### 1. **数组的声明和初始化**
Java 数组的声明和初始化有多种方式。

#### 1.1 **声明数组**
可以使用 `[]` 来声明数组。

```java
// 声明一个整数数组
int[] numbers;
```

#### 1.2 **初始化数组**
可以在声明时进行初始化，或者在声明后通过 `new` 关键字来分配空间。

```java
// 通过指定大小初始化数组
int[] numbers = new int[5];  // 创建一个长度为 5 的整数数组，默认值为 0

// 使用初始化列表初始化数组
int[] numbers = {1, 2, 3, 4, 5};  // 创建一个包含 5 个整数的数组
```

#### 1.3 **多维数组**
Java 支持多维数组。可以创建二维数组、三维数组等。

```java
// 声明并初始化二维数组
int[][] matrix = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};  // 创建 3x3 矩阵

// 使用 new 创建二维数组
int[][] matrix = new int[3][3];  // 创建一个 3x3 的二维数组
```

### 2. **访问数组元素**
数组的元素可以通过索引访问。索引从 0 开始。

```java
int[] numbers = {10, 20, 30, 40, 50};

// 访问数组的第一个元素
System.out.println(numbers[0]);  // 输出 10

// 访问数组的第三个元素
System.out.println(numbers[2]);  // 输出 30
```

### 3. **数组的长度**
数组的长度可以通过 `.length` 属性获取。

```java
int[] numbers = {1, 2, 3, 4, 5};
System.out.println("Array length: " + numbers.length);  // 输出 5
```

### 4. **数组的遍历**
数组可以通过常规的 `for` 循环或增强的 `for-each` 循环来遍历。

#### 4.1 **普通 `for` 循环**
```java
int[] numbers = {1, 2, 3, 4, 5};

for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);  // 输出每个元素
}
```

#### 4.2 **增强的 `for-each` 循环**
```java
int[] numbers = {1, 2, 3, 4, 5};

for (int num : numbers) {
    System.out.println(num);  // 输出每个元素
}
```

### 5. **数组的默认值**
对于不同的数据类型，数组的默认值如下：
- **基本数据类型数组**：`int` 默认值为 0，`boolean` 默认值为 `false`，`char` 默认值为 `'\u0000'`，`float` 默认值为 `0.0f`，`double` 默认值为 `0.0`。
- **对象数组**：所有对象的默认值为 `null`。

#### 示例：
```java
int[] numbers = new int[5];
System.out.println(numbers[0]);  // 输出 0
```

### 6. **数组的复制**
可以使用 `System.arraycopy()` 方法来复制数组，也可以使用 `Arrays.copyOf()`。

#### 示例：`System.arraycopy()`
```java
int[] original = {1, 2, 3, 4, 5};
int[] copy = new int[original.length];
System.arraycopy(original, 0, copy, 0, original.length);

for (int num : copy) {
    System.out.println(num);  // 输出 1 2 3 4 5
}
```

#### 示例：`Arrays.copyOf()`
```java
import java.util.Arrays;

int[] original = {1, 2, 3, 4, 5};
int[] copy = Arrays.copyOf(original, original.length);

for (int num : copy) {
    System.out.println(num);  // 输出 1 2 3 4 5
}
```

### 7. **数组的排序**
可以使用 `Arrays.sort()` 方法对数组进行排序。

#### 示例：
```java
import java.util.Arrays;

int[] numbers = {5, 3, 8, 1, 2};
Arrays.sort(numbers);

for (int num : numbers) {
    System.out.println(num);  // 输出 1 2 3 5 8
}
```

### 8. **数组的常见操作**
- **查找最大/最小值**：可以使用循环或者 `Arrays.stream()` 来查找。
- **查找元素是否存在**：可以使用 `Arrays.asList()` 将数组转换为列表，使用 `contains()` 方法来判断。

#### 示例：查找最大值
```java
int[] numbers = {5, 3, 8, 1, 2};
int max = numbers[0];

for (int i = 1; i < numbers.length; i++) {
    if (numbers[i] > max) {
        max = numbers[i];
    }
}

System.out.println("Max value: " + max);  // 输出 8
```

### 9. **多维数组的访问**
对于多维数组，可以通过嵌套的索引访问。

#### 示例：访问二维数组元素
```java
int[][] matrix = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
System.out.println(matrix[0][0]);  // 输出 1
System.out.println(matrix[2][2]);  // 输出 9
```
