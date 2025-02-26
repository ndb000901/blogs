# lombok 基础

## 1.添加依赖(Maven)

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.36</version>
    <scope>provided</scope>
</dependency>
```

## 2.IDEA设置

- Intellij IDEA(MacOS) 设置支持注解处理

  - 点击 Intellij IDEA > Settings > Build > Compiler > Annotation Processors

  - 勾选 Enable annotation processing

- 安装[lombok插件](https://plugins.jetbrains.com/plugin/6317-lombok)


## 2.使用教程

Lombok 是一个 Java 的代码简化工具，它通过注解（Annotations）自动生成样板代码（如 `getter`、`setter`、`toString` 等），减少手写代码，提高开发效率。

---

## **1. Getter、Setter 相关注解**
### 1.1 `@Getter`
- 作用：自动为类中的所有字段（或指定字段）生成 `getter` 方法。
- 位置：可以用于**类**或**字段**。
- 示例：
  ```java
    import lombok.Getter;
    
    public class User {
    
        @Getter
        private String name;
    
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    
    public class User {
        private String name;
        private int age;
    
        public User() {
        }
    
        @Generated
        public String getName() {
            return this.name;
        }
    }
  ```

### 1.2 `@Setter`
- 作用：自动为类中的所有字段（或指定字段）生成 `setter` 方法。
- 位置：可以用于**类**或**字段**。
- 示例：
  ```java
    import lombok.Setter;
    
    public class User {
    
        @Setter
        private String name;
    
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    
    public class User {
        private String name;
        private int age;
    
        public User() {
        }
    
        @Generated
        public void setName(String name) {
            this.name = name;
        }
    }
  ```

---

## **2. 构造方法相关注解**
### 2.1 `@NoArgsConstructor`
- 作用：生成**无参**构造方法。
- 适用场景：需要无参构造器（如 Spring 反射实例化对象时）。
- 示例：
  ```java
    import lombok.NoArgsConstructor;
    
    @NoArgsConstructor
    public class User {
    
        private String name;
    
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    
    public class User {
        private String name;
        private int age;
    
        @Generated
        public User() {
        }
    }
  ```

### 2.2 `@AllArgsConstructor`
- 作用：生成**全参数**构造方法。
- 示例：
  ```java
    import lombok.AllArgsConstructor;
    
    @AllArgsConstructor
    public class User {
    
        private String name;
    
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    
    public class User {
        private String name;
        private int age;
    
        @Generated
        public User(String name, int age) {
            this.name = name;
            this.age = age;
        }
    }
  ```

### 2.3 `@RequiredArgsConstructor`
- 作用：生成**包含 `final` 或 `@NonNull` 字段**的构造方法。
- 示例：
  ```java
    import lombok.NonNull;
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    public class User {
    
        private final String name;
    
        @NonNull
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    import lombok.NonNull;
    
    public class User {
        private final String name;
        private @NonNull int age;
    
        @Generated
        public User(String name, @NonNull int age) {
            this.name = name;
            this.age = age;
        }
    }
  ```

---

## **3. `toString`、`equals`、`hashCode` 相关注解**
### 3.1 `@ToString`
- 作用：自动生成 `toString()` 方法。
- 示例：
  ```java
    import lombok.ToString;
    
    @ToString
    public class User {
    
        private String name;
    
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    
    public class User {
        private String name;
        private int age;
    
        public User() {
        }
    
        @Generated
        public String toString() {
            return "User(name=" + this.name + ", age=" + this.age + ")";
        }
    }
  ```

- **排除某些字段**：
  ```java
  @ToString(exclude = "name")
  private String name;
  ```

### 3.2 `@EqualsAndHashCode`
- 作用：自动生成 `equals()` 和 `hashCode()` 方法。
- 示例：
  ```java
    import lombok.EqualsAndHashCode;
    
    @EqualsAndHashCode
    public class User {
    
        private String name;
    
        private int age;
    }
  ```
- 编译后
  ```java
    import lombok.Generated;
    
    public class User {
        private String name;
        private int age;
    
        public User() {
        }
    
        @Generated
        public boolean equals(Object o) {
            if (o == this) {
                return true;
            } else if (!(o instanceof User)) {
                return false;
            } else {
                User other = (User)o;
                if (!other.canEqual(this)) {
                    return false;
                } else if (this.age != other.age) {
                    return false;
                } else {
                    Object this$name = this.name;
                    Object other$name = other.name;
                    if (this$name == null) {
                        if (other$name != null) {
                            return false;
                        }
                    } else if (!this$name.equals(other$name)) {
                        return false;
                    }
    
                    return true;
                }
            }
        }
    
        @Generated
        protected boolean canEqual(Object other) {
            return other instanceof User;
        }
    
        @Generated
        public int hashCode() {
            int PRIME = 59;
            int result = 1;
            result = result * 59 + this.age;
            Object $name = this.name;
            result = result * 59 + ($name == null ? 43 : $name.hashCode());
            return result;
        }
    }
  ```

---

## **4. `Data` 和 `Value` 组合注解**
### 4.1 `@Data`
- 作用：相当于 `@Getter`、`@Setter`、`@ToString`、`@EqualsAndHashCode`、`@RequiredArgsConstructor` 组合。
- 示例：
  ```java
    import lombok.Data;
    
    @Data
    public class User {
    
        private String name;
    
        private int age;
    }
  ```
  该注解等同于：
  ```java
  @Getter
  @Setter
  @ToString
  @EqualsAndHashCode
  @RequiredArgsConstructor
  public class User { ... }
  ```
- 编译后
  ```java
    import lombok.Generated;

    public class User {
        private String name;
        private int age;
    
        @Generated
        public User() {
        }
    
        @Generated
        public String getName() {
            return this.name;
        }
    
        @Generated
        public int getAge() {
            return this.age;
        }
    
        @Generated
        public void setName(String name) {
            this.name = name;
        }
    
        @Generated
        public void setAge(int age) {
            this.age = age;
        }
    
        @Generated
        public boolean equals(Object o) {
            if (o == this) {
                return true;
            } else if (!(o instanceof User)) {
                return false;
            } else {
                User other = (User)o;
                if (!other.canEqual(this)) {
                    return false;
                } else if (this.getAge() != other.getAge()) {
                    return false;
                } else {
                    Object this$name = this.getName();
                    Object other$name = other.getName();
                    if (this$name == null) {
                        if (other$name != null) {
                            return false;
                        }
                    } else if (!this$name.equals(other$name)) {
                        return false;
                    }
    
                    return true;
                }
            }
        }
    
        @Generated
        protected boolean canEqual(Object other) {
            return other instanceof User;
        }
    
        @Generated
        public int hashCode() {
            int PRIME = 59;
            int result = 1;
            result = result * 59 + this.getAge();
            Object $name = this.getName();
            result = result * 59 + ($name == null ? 43 : $name.hashCode());
            return result;
        }
    
        @Generated
        public String toString() {
            String var10000 = this.getName();
            return "User(name=" + var10000 + ", age=" + this.getAge() + ")";
        }
    }
  ```

### 4.2 `@Value`
- 作用：**不可变对象（类的所有字段 `private final`）**，类似 `@Data` 但不生成 `setter`。
- 示例：
  ```java
    import lombok.Value;
    
    @Value
    public class User {
    
        private String name;
    
        private int age;
    }
  ```
- 该注解等同于：
  - 所有字段 `final`
  - 没有 `setter`
  - 生成 `getter`
  - 生成 `toString()`
  - 生成 `equals()` 和 `hashCode()`

- 编译后
  ```java
    import lombok.Generated;

    public final class User {
        private final String name;
        private final int age;
    
        @Generated
        public User(String name, int age) {
            this.name = name;
            this.age = age;
        }
    
        @Generated
        public String getName() {
            return this.name;
        }
    
        @Generated
        public int getAge() {
            return this.age;
        }
    
        @Generated
        public boolean equals(Object o) {
            if (o == this) {
                return true;
            } else if (!(o instanceof User)) {
                return false;
            } else {
                User other = (User)o;
                if (this.getAge() != other.getAge()) {
                    return false;
                } else {
                    Object this$name = this.getName();
                    Object other$name = other.getName();
                    if (this$name == null) {
                        if (other$name != null) {
                            return false;
                        }
                    } else if (!this$name.equals(other$name)) {
                        return false;
                    }
    
                    return true;
                }
            }
        }
    
        @Generated
        public int hashCode() {
            int PRIME = 59;
            int result = 1;
            result = result * 59 + this.getAge();
            Object $name = this.getName();
            result = result * 59 + ($name == null ? 43 : $name.hashCode());
            return result;
        }
    
        @Generated
        public String toString() {
            String var10000 = this.getName();
            return "User(name=" + var10000 + ", age=" + this.getAge() + ")";
        }
    }
  ```
---

## **5. 日志相关注解**
### 5.1 `@Log4j2`
- 作用：自动创建 `private static final Logger log = LogManager.getLogger(User.class);` 变量。
- 示例：
  ```java
  import lombok.extern.log4j.Log4j2;
  
  @Log4j2
  public class User {
  
      private String name;
  
      private int age;
  
      public void log() {
          log.debug("hello log");
      }
  }
  ```
- 编译后
  ```java
  import lombok.Generated;
  import org.apache.logging.log4j.LogManager;
  import org.apache.logging.log4j.Logger;
  
  public class User {
      @Generated
      private static final Logger log = LogManager.getLogger(User.class);
      private String name;
      private int age;
  
      public User() {
      }
  
      public void log() {
          log.debug("hello log");
      }
  }
  ```

---

## **6. `Builder` 相关注解**
### 6.1 `@Builder`
- 作用：实现 **Builder 设计模式**。
- 示例：
  ```java
  import lombok.Builder;
  
  @Builder
  public class User {
  
      private String name;
  
      private int age;
  
  }
  ```
- 编译后
  ```java
  import lombok.Generated;
  
  public class User {
      private String name;
      private int age;
  
      @Generated
      User(String name, int age) {
          this.name = name;
          this.age = age;
      }
  
      @Generated
      public static UserBuilder builder() {
          return new UserBuilder();
      }
  
      @Generated
      public static class UserBuilder {
          @Generated
          private String name;
          @Generated
          private int age;
  
          @Generated
          UserBuilder() {
          }
  
          @Generated
          public UserBuilder name(String name) {
              this.name = name;
              return this;
          }
  
          @Generated
          public UserBuilder age(int age) {
              this.age = age;
              return this;
          }
  
          @Generated
          public User build() {
              return new User(this.name, this.age);
          }
  
          @Generated
          public String toString() {
              return "User.UserBuilder(name=" + this.name + ", age=" + this.age + ")";
          }
      }
  }
  ```

### 6.2 `@Singular`
- 作用：用于 `@Builder`，避免集合字段重复赋值。
- 示例：
  ```java
  import java.util.List;
  import lombok.Builder;
  import lombok.Singular;
  
  @Builder
  public class User {
  
      private String name;
  
      private int age;
  
      @Singular
      private List<String> books;
  
  }
  ```
- 编译后
  ```java
  
  import java.util.ArrayList;
  import java.util.Collection;
  import java.util.Collections;
  import java.util.List;
  import lombok.Generated;
  
  public class User {
      private String name;
      private int age;
      private List<String> books;
  
      @Generated
      User(String name, int age, List<String> books) {
          this.name = name;
          this.age = age;
          this.books = books;
      }
  
      @Generated
      public static UserBuilder builder() {
          return new UserBuilder();
      }
  
      @Generated
      public static class UserBuilder {
          @Generated
          private String name;
          @Generated
          private int age;
          @Generated
          private ArrayList<String> books;
  
          @Generated
          UserBuilder() {
          }
  
          @Generated
          public UserBuilder name(String name) {
              this.name = name;
              return this;
          }
  
          @Generated
          public UserBuilder age(int age) {
              this.age = age;
              return this;
          }
  
          @Generated
          public UserBuilder book(String book) {
              if (this.books == null) {
                  this.books = new ArrayList();
              }
  
              this.books.add(book);
              return this;
          }
  
          @Generated
          public UserBuilder books(Collection<? extends String> books) {
              if (books == null) {
                  throw new NullPointerException("books cannot be null");
              } else {
                  if (this.books == null) {
                      this.books = new ArrayList();
                  }
  
                  this.books.addAll(books);
                  return this;
              }
          }
  
          @Generated
          public UserBuilder clearBooks() {
              if (this.books != null) {
                  this.books.clear();
              }
  
              return this;
          }
  
          @Generated
          public User build() {
              List<String> books;
              switch (this.books == null ? 0 : this.books.size()) {
                  case 0 -> books = Collections.emptyList();
                  case 1 -> books = Collections.singletonList((String)this.books.get(0));
                  default -> books = Collections.unmodifiableList(new ArrayList(this.books));
              }
  
              return new User(this.name, this.age, books);
          }
  
          @Generated
          public String toString() {
              String var10000 = this.name;
              return "User.UserBuilder(name=" + var10000 + ", age=" + this.age + ", books=" + String.valueOf(this.books) + ")";
          }
      }
  }

  ```

---

## **7. 其他注解**
### 7.1 `@SneakyThrows`
- 作用：自动抛出异常（无需 `try-catch`）。
- 示例：
  ```java
  import lombok.SneakyThrows;
  
  public class User {
  
      private String name;
  
      private int age;
  
      @SneakyThrows
      public void test() {
          int a = 1 / 0;
      }
  
  }
  ```
- 编译后
  ```java
  public class User {
    private String name;
    private int age;

    public User() {
    }

    public void test() {
        try {
            int $ex = 1 / 0;
        } catch (Throwable $ex) {
            throw $ex;
        }
    }
  }
  ```
  

### 7.2 `@Cleanup`
- 作用：自动调用 `close()` 方法（适用于 `InputStream`、`OutputStream` 等）。
- 示例：
  ```java
  import lombok.Cleanup;
  import lombok.SneakyThrows;
  import java.io.FileInputStream;
  
  public class User {
  
      private String name;
  
      private int age;

      @SneakyThrows
      public void readBook() {
          @Cleanup
          FileInputStream inputStream = new FileInputStream("hello.txt");
  
      }
  
  }
  ```
- 编译后
  ```java
  import java.io.FileInputStream;
  import java.util.Collections;
  
  public class User {
      private String name;
      private int age;
  
      public User() {
      }
  
      public void readBook() {
          try {
              FileInputStream inputStream = new FileInputStream("hello.txt");
              if (Collections.singletonList(inputStream).get(0) != null) {
                  inputStream.close();
              }
  
          } catch (Throwable $ex) {
              throw $ex;
          }
      }
  }
  ```
---


