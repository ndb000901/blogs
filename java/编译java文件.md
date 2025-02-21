# 编译java文件

## 1.源文件

`Hello.java`

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

## 2.编译

```bash

javac Hello.java
```

## 3.运行

```bash

java -cp ./ Hello
```

### 若源码包含`package`

`Hello.java`

```java
package com.mycompany.app;
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

### 编译运行

```bash
# 编译
javac -d . Hello.javac

# 运行

java -cp ./ com.mycompany.app.Hello
```