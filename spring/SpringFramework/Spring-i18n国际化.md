# Spring i18n 国际化
国际化（Internationalization，简称 **i18n**）是指让应用程序支持 **多种语言** 和 **不同地区格式**（如时间、货币、数字等）的能力。Spring 提供了 **`MessageSource` 机制** 来支持国际化，让应用根据用户的语言环境动态选择不同的文本内容。

## 1. Spring i18n 关键组件
Spring 主要使用 **`MessageSource`** 及其实现类 **`ResourceBundleMessageSource`** 或 **`ReloadableResourceBundleMessageSource`** 来进行国际化。

| 组件 | 作用 |
|------|------|
| `MessageSource` | Spring 的国际化接口，提供 `getMessage()` 方法获取多语言信息 |
| `ResourceBundleMessageSource` | 读取 `classpath` 目录下的 `.properties` 资源文件 |
| `ReloadableResourceBundleMessageSource` | 类似 `ResourceBundleMessageSource`，但支持**热更新**，可动态加载最新的国际化配置 |
| `LocaleResolver` | 解析用户请求的语言环境（`Locale`） |
| `SessionLocaleResolver` | 通过 **Session** 解析 `Locale` |
| `AcceptHeaderLocaleResolver` | 通过 **HTTP 头部 `Accept-Language`** 解析 `Locale`（Spring Boot 默认使用） |

---

## 2. 配置i18n资源

在 `resources` 目录下创建 **国际化 `.properties` 文件**，文件名格式：  

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

## 3.源码

`AppConfig.java`
```java
package com.hello.demo.spring.i18n.config;

import org.springframework.context.MessageSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.ResourceBundleMessageSource;

@Configuration
@ComponentScan("com.hello")
public class AppConfig {

    @Bean
    public MessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        messageSource.setBasename("messages");
        messageSource.setDefaultEncoding("UTF-8");
        return messageSource;
    }
}

```

`BeanService.java`
```java
package com.hello.demo.spring.i18n.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.stereotype.Service;

import java.util.Locale;

@Service
public class BeanService {

    @Autowired
    private MessageSource messageSource;

    public void getMessage() {
        System.out.println(this.messageSource.getMessage("yes_button", null, Locale.CHINA));
        System.out.println(this.messageSource.getMessage("yes_button", null, Locale.US));
    }

}

```

`I18nDemo.java`
```java
package com.hello.demo.spring.i18n;

import com.hello.demo.spring.i18n.config.AppConfig;
import com.hello.demo.spring.i18n.service.BeanService;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class I18nDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        BeanService beanService = (BeanService) context.getBean("beanService");
        beanService.getMessage();
    }
}
```
