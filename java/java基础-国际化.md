# java基础-国际化

在 Java 中，国际化（i18n）通常通过 `ResourceBundle` 和 `Locale` 结合 `.properties` 文件来实现。

---

## 1. 使用 Java 原生 `ResourceBundle` 进行国际化

创建国际化资源文件

在 `resources` 目录下创建 `.properties` 资源文件：


`messages_en_US.properties`
```properties
yes_button=Yes
no_button=No
```

`messages_zh_CN.properties`
```properties
yes_button=是
no_button=否
```


## 2 在 Java 代码中读取国际化资源
```java
package com.hello.demo.spring.i18n;

import java.util.Locale;
import java.util.ResourceBundle;

public class JavaI18nDemo {

    public static void main(String[] args) {

        Locale defaultLocale = Locale.getDefault();
        ResourceBundle resourceBundle = ResourceBundle.getBundle("messages", defaultLocale);
        System.out.println(resourceBundle.getString("yes_button"));

        Locale usLocale = new Locale("en", "US");
        resourceBundle = ResourceBundle.getBundle("messages", usLocale);
        System.out.println(resourceBundle.getString("yes_button"));

        Locale zhLocale = new Locale("zh", "CN");
        resourceBundle = ResourceBundle.getBundle("messages", zhLocale);
        System.out.println(resourceBundle.getString("yes_button"));
    }
}

```

