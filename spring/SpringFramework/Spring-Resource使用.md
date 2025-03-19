# Spring Resource使用

在 Spring 中，`Resource` 是用于**统一访问不同类型资源（文件、类路径、URL、流等）**的抽象接口，提供了一种**更通用、更灵活的资源加载方式**。

---

## **1. `Resource` 的核心概念**
Spring 提供了 `org.springframework.core.io.Resource` 接口，该接口是对**不同类型资源的抽象**，主要用于加载：
- **文件系统资源**（File）
- **类路径资源**（Classpath）
- **URL 资源**（远程或本地）
- **流资源**（InputStream）
- **数据库 BLOB 资源**（数据库中的二进制数据）
- **嵌套 JAR 资源**（Spring Boot Fat JAR）
- 其他自定义资源类型

### **`Resource` 接口的常见方法**
| 方法 | 作用 |
|------|------|
| `exists()` | 检查资源是否存在 |
| `getInputStream()` | 获取资源输入流 |
| `getFilename()` | 获取资源名称 |
| `getDescription()` | 获取资源描述信息 |
| `getFile()` | 将资源转换为 `File` 对象（若适用） |
| `getURI()` | 获取资源的 URI |
| `getURL()` | 获取资源的 URL |

---

## **2. `Resource` 的常见实现类**
Spring 提供了多种 `Resource` 实现类，它们适用于不同的资源类型：

| 实现类 | 适用场景 |
|--------|----------|
| `ClassPathResource` | 读取类路径下的资源，例如 `classpath:/config.properties` |
| `FileSystemResource` | 读取文件系统中的资源，例如 `/home/user/data.txt` |
| `UrlResource` | 读取远程或本地 URL 资源，例如 `http://example.com/file.txt` |
| `InputStreamResource` | 基于 `InputStream` 直接创建资源 |
| `ByteArrayResource` | 基于 `byte[]` 直接创建资源 |
| `ServletContextResource` | 用于在 Web 应用中访问 `ServletContext` 资源 |
| `PathResource` | 适用于 JDK 7+ 的 `java.nio.file.Path` 资源 |

---

## **3. `ResourceLoader` 统一资源加载**
Spring 通过 `org.springframework.core.io.ResourceLoader` 统一管理 `Resource` 的创建，它可以根据不同的路径前缀**自动解析合适的 `Resource` 实现**。

### **资源路径前缀**
| 前缀 | 解析方式 |
|------|----------|
| `classpath:` | 加载类路径下的资源（使用 `ClassPathResource`） |
| `file:` | 加载文件系统中的资源（使用 `FileSystemResource`） |
| `http:` / `https:` | 加载远程 URL 资源（使用 `UrlResource`） |
| **无前缀** | 默认根据上下文决定（通常为 `FileSystemResource` 或 `ClassPathResource`） |

### **示例代码**
```java
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.core.io.DefaultResourceLoader;

public class ResourceLoaderExample {
    public static void main(String[] args) throws Exception {
        ResourceLoader resourceLoader = new DefaultResourceLoader();

        // 加载类路径资源
        Resource classpathResource = resourceLoader.getResource("classpath:config.properties");

        // 加载文件系统资源
        Resource fileResource = resourceLoader.getResource("file:/home/user/data.txt");

        // 加载远程 URL 资源
        Resource urlResource = resourceLoader.getResource("http://example.com/file.txt");

        System.out.println("Classpath Resource exists: " + classpathResource.exists());
        System.out.println("File Resource exists: " + fileResource.exists());
        System.out.println("URL Resource exists: " + urlResource.exists());
    }
}
```

---

## **4. `@Value` 结合 `Resource` 进行注入**
Spring 允许直接通过 `@Value` 注解注入 `Resource` 类型的对象。

### **示例：加载 classpath 资源**
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.Resource;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

@Component
public class ResourceBean {

    @Value("classpath:config.properties")
    private Resource configFile;

    public void printResourceContent() throws IOException {
        String content = new String(Files.readAllBytes(Paths.get(configFile.getURI())));
        System.out.println("Config File Content: " + content);
    }
}
```

---

## **5. `ResourcePatternResolver` 批量匹配资源**
`org.springframework.core.io.support.ResourcePatternResolver` 扩展了 `ResourceLoader`，支持**通配符匹配**来批量加载资源。

### **常见通配符**
| 通配符 | 作用 |
|--------|------|
| `*` | 匹配文件名中的任意字符（不含 `/`） |
| `**` | 匹配任意子目录 |
| `?` | 匹配单个字符 |

### **示例：批量加载 classpath 下的 XML 文件**
```java
import org.springframework.core.io.Resource;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

import java.io.IOException;

public class ResourcePatternExample {
    public static void main(String[] args) throws IOException {
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();

        // 批量加载 classpath:config 目录下所有 XML 文件
        Resource[] resources = resolver.getResources("classpath:config/*.xml");

        for (Resource resource : resources) {
            System.out.println("Found resource: " + resource.getFilename());
        }
    }
}
```

---

## **6. `ApplicationContext` 作为 `ResourceLoader`**
`ApplicationContext` 继承了 `ResourceLoader`，在 Spring 应用中可以直接使用 `ApplicationContext` 加载资源。

### **示例**
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.core.io.Resource;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

public class ApplicationContextResourceExample {
    public static void main(String[] args) throws IOException {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        // 直接从 ApplicationContext 获取资源
        Resource resource = context.getResource("classpath:config.properties");

        // 读取文件内容
        String content = new String(Files.readAllBytes(Paths.get(resource.getURI())));
        System.out.println("File Content: " + content);
    }
}
```

## 7.ResourceLoaderAware

实现`ResourceLoaderAware`接口，获取`ResourceLoader`实例

```java

package com.hello.demo.spring.resource.service;

import org.springframework.context.ResourceLoaderAware;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.stereotype.Service;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

@Service
public class BeanService implements ResourceLoaderAware {

    private ResourceLoader resourceLoader;

    @Override
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }

    public void getBean() {
        Resource resource = this.resourceLoader.getResource("classpath:bean.xml");
        System.out.println(resource.exists());

        try (BufferedReader reader = new BufferedReader(
                new InputStreamReader(resource.getInputStream())
        )) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```
