# java基础-命名方式

在Java中，命名规范（命名约定）对于代码的可读性、可维护性和一致性非常重要。以下是Java中常见的命名约定，涵盖了变量、类、文件等的命名方式。

### 1. **变量命名**（Variable Naming）
- **命名规则**：
  - **小写字母**：变量名通常以小写字母开头，多个单词时使用驼峰命名法（camelCase），即后续单词的首字母大写。
  - **避免使用单个字母**：除非在循环计数器等非常短的上下文中，尽量避免单个字母作为变量名。
  - **有意义的名称**：变量名应该简洁且有意义，能够反映变量的用途。

- **示例**：
  ```java
  int studentCount;  // 变量名为 studentCount
  String firstName;  // 变量名为 firstName
  ```

- **常量命名**：
  - 常量的命名通常使用 **全大写字母**，并使用下划线分隔单词。
  - 常量应使用 `final` 修饰符，表示它的值不可更改。
  
- **示例**：
  ```java
  public static final int MAX_VALUE = 100;
  public static final String DEFAULT_NAME = "Unnamed";
  ```

### 2. **类命名**（Class Naming）
- **命名规则**：
  - **大写字母**：类名采用 **帕斯卡命名法**（PascalCase），即每个单词的首字母大写，没有下划线。
  - 类名应具有描述性，通常为名词。
  - Java中类名通常是 **名词** 或者 **名词短语**，并且反映类的作用或功能。

- **示例**：
  ```java
  public class Student {  // 类名为 Student
      private String name;
      private int age;
  }
  
  public class CarModel {  // 类名为 CarModel
      private String modelName;
      private int year;
  }
  ```

### 3. **接口命名**（Interface Naming）
- **命名规则**：
  - 接口名通常使用 **帕斯卡命名法**，与类名命名方式类似。
  - 另外，接口名最好能体现“行为”或者“功能”。
  - 接口名常常以 “I” 或 “able” 作为后缀（虽然这不是强制性的，但某些团队或项目中可能有这种约定）。

- **示例**：
  ```java
  public interface Readable {  // 接口命名为 Readable，表示可读
      void read();
  }

  public interface Runnable {  // 接口命名为 Runnable，表示可以执行的
      void run();
  }
  ```

### 4. **方法命名**（Method Naming）
- **命名规则**：
  - 方法名采用 **小写字母开头的驼峰命名法**。
  - 方法名通常是动词或动词短语，表示该方法的操作或行为。
  - 方法名应简洁且具有描述性。

- **示例**：
  ```java
  public void calculateSum() {  // 方法命名为 calculateSum
      // 执行加法操作
  }

  public void printDetails() {  // 方法命名为 printDetails
      // 打印详情
  }
  ```

### 5. **文件命名**（File Naming）
- **命名规则**：
  - Java源文件的名称应与类名一致（区分大小写），并且以 `.java` 为后缀。
  - 如果文件包含多个类，通常只保留一个公共类（`public class`），该类的名称必须与文件名一致。
  - 类和接口的文件名应遵循类名和接口名的命名规则。

- **示例**：
  - 对应 `Student` 类的源文件应命名为 `Student.java`。
  - 对应 `CarModel` 类的源文件应命名为 `CarModel.java`。

### 6. **包命名**（Package Naming）
- **命名规则**：
  - 包名应全部使用小写字母。
  - 使用公司的域名反转形式（例如：`com.companyname.projectname`）作为基础包名。
  - 包名通常以功能或模块的名称来区分，例如 `com.companyname.projectname.model`、`com.companyname.projectname.service` 等。

- **示例**：
  ```java
  package com.example.student;
  
  public class Student {  // 类名为 Student
      private String name;
      private int age;
  }
  ```

### 7. **枚举命名**（Enum Naming）
- **命名规则**：
  - 枚举类的命名使用 **帕斯卡命名法**，每个枚举常量使用 **大写字母**，并用下划线分隔。
  
- **示例**：
  ```java
  public enum Day {
      MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
  }
  ```
