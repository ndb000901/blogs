# Maven 生命周期

Maven 的生命周期（Lifecycle）定义了项目构建的整个流程，主要包括 **Clean、Default（Build）、Site** 三个生命周期，每个生命周期包含多个阶段（Phase）。

---

## **1. Clean 生命周期**
用于清理项目的构建目录，主要包含：
- `pre-clean`：执行清理前的工作
- `clean`：删除 `target` 目录，清除编译产物
- `post-clean`：执行清理后的工作

> **示例：**
```sh

mvn clean
```
清理 `target/` 目录。

---

## **2. Default（Build）生命周期**
用于完成项目的编译、测试、打包、部署等，包含多个阶段：
1. **`validate`**：验证项目是否正确
2. **`initialize`**：初始化构建状态，例如设置属性
3. **`generate-sources`**：生成源代码（如果有代码生成插件）
4. **`process-sources`**：处理源代码，如代码格式化
5. **`generate-resources`**：生成资源文件
6. **`process-resources`**：复制/过滤资源文件到 `target/classes`
7. **`compile`**：编译 Java 源码到 `target/classes`
8. **`process-classes`**：对编译后的 class 进行处理，如 AOP
9. **`generate-test-sources`**：生成测试代码
10. **`process-test-sources`**：处理测试代码
11. **`generate-test-resources`**：生成测试资源
12. **`process-test-resources`**：复制测试资源
13. **`test-compile`**：编译测试代码
14. **`process-test-classes`**：处理测试类
15. **`test`**：运行单元测试
16. **`prepare-package`**：准备打包工作
17. **`package`**：打包（如生成 JAR/WAR）
18. **`pre-integration-test`**：集成测试前准备
19. **`integration-test`**：运行集成测试
20. **`post-integration-test`**：集成测试后清理
21. **`verify`**：验证打包是否完整
22. **`install`**：安装 JAR/WAR 到本地仓库
23. **`deploy`**：部署到远程仓库（通常是 Nexus）

> **示例：**
```sh

mvn package
```
执行 `validate` -> `compile` -> `test` -> `package`。

---

## **3. Site 生命周期**
用于生成和发布项目文档：
- `pre-site`：执行文档生成前的操作
- `site`：生成项目文档
- `post-site`：执行文档生成后的操作
- `site-deploy`：发布项目站点到服务器

> **示例：**
```sh

mvn site
```
生成 `target/site/index.html`。

---

## **4. 生命周期执行特点**
1. **生命周期是有序的**：执行 `mvn package`，会自动执行之前所有阶段。
2. **可以跳过某些阶段**：
   ```sh
   
   mvn package -DskipTests
   ```
   跳过 `test`，直接 `package`。
3. **默认插件绑定**：
   - `compile` 绑定 `maven-compiler-plugin`
   - `test` 绑定 `maven-surefire-plugin`
   - `package` 绑定 `maven-jar-plugin`
   - `install` 绑定 `maven-install-plugin`
   - `deploy` 绑定 `maven-deploy-plugin`

---

## **总结**
| 生命周期 | 关键阶段 | 作用 |
|---------|--------|------|
| **Clean** | clean | 清理 `target/` 目录 |
| **Default（Build）** | compile、test、package、install、deploy | 编译、测试、打包、安装和部署 |
| **Site** | site、site-deploy | 生成和发布文档 |
