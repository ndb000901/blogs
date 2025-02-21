# maven创建工程

## 1.官方示例

[文档](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)


## 2.新建项目

```bash

mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.5 -DinteractiveMode=false
```

### **1. `mvn archetype:generate`**
`archetype:generate` 是 **Maven Archetype 插件** 的一个目标（goal），它用于 **创建新的 Maven 项目**，基于一个 **预定义的模板**（archetype）。

### **2. `-DgroupId=com.mycompany.app`**
- **`groupId`** 是 **组织唯一标识符**，通常采用 **反向域名** 结构，比如 `com.mycompany.app`。
- 作用：
  - 在 **Maven 依赖** 中，`groupId` 是唯一的，用于区分不同的组织或公司发布的软件包。
  - **示例：**
    ```xml
    <dependency>
        <groupId>com.mycompany.app</groupId>
        <artifactId>my-app</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    ```

### **3. `-DartifactId=my-app`**
- **`artifactId`** 是 **项目的名称**，在 `pom.xml` 里也会用到。
- 生成的项目目录名就是 `my-app/`。
- 这个 **`artifactId`** 也决定了 **最终生成的 JAR 或 WAR 名称**：
  ```
  target/my-app-1.0-SNAPSHOT.jar
  ```

### **4. `-DarchetypeArtifactId=maven-archetype-quickstart`**
- 选择 **Maven 原型 (archetype)**，这里使用的是 `maven-archetype-quickstart`，它是 **最常用的 Java 项目模板**。
- 这个模板会自动创建：
  - `src/main/java/`（Java 代码）
  - `src/test/java/`（单元测试代码）
  - `pom.xml`（Maven 构建文件）

### **5. `-DarchetypeVersion=1.5`**
- 选择 **archetype 版本**，这里使用 `1.5` 版（最新版本可能不同）。

### **6. `-DinteractiveMode=false`**
- 这个参数 **关闭交互模式**，直接使用上面提供的参数 **自动生成项目**。
- 如果不加这个参数，Maven 会进入 **交互模式**，要求用户手动确认 `groupId`、`artifactId` 等。

### **总结**
| 命令 | 作用 |
|------|------|
| `mvn archetype:generate` | 生成一个新的 Maven 项目 |
| `-DgroupId=com.mycompany.app` | 设置 `groupId`（类似命名空间） |
| `-DartifactId=my-app` | 设置 `artifactId`（项目名称） |
| `-DarchetypeArtifactId=maven-archetype-quickstart` | 使用 `quickstart` 模板 |
| `-DinteractiveMode=false` | 关闭交互模式，自动生成 |

## 3.如何运行项目

### **1. 进入项目目录**

```bash

cd my-app
```

### **2. 编译项目**

```bash

mvn compile
```
Maven 会下载依赖并编译 `src/main/java` 代码。

### **3. 运行测试**

```bash

mvn test
```
Maven 会运行 `src/test/java` 里的 JUnit 测试。

### **4. 打包项目**

```bash

mvn package
```
- 生成的 JAR 文件在 `target/` 目录：
  ```
  target/my-app-1.0-SNAPSHOT.jar
  ```

## 4.模版

[文档](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)



