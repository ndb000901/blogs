# java基础-枚举

在 Java 中，**枚举（`enum`）** 是一种特殊的数据类型，它代表一组固定的常量。使用枚举可以更清晰地表示有限的选择值，使代码更加易于理解和维护。枚举是 Java 中一种类型安全的方式来定义常量集合。

### 1. **基本定义**
Java 中的枚举是通过关键字 `enum` 来定义的，通常用于定义固定集合的常量。

#### 示例：
```java
public enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

在这个例子中，`Day` 是一个枚举类型，包含了七个常量：`MONDAY`，`TUESDAY`，`WEDNESDAY`，`THURSDAY`，`FRIDAY`，`SATURDAY`，`SUNDAY`。

### 2. **使用枚举**
一旦定义了枚举类型，就可以使用枚举的常量来代替传统的常量定义。枚举类型实例不能被创建，只能通过预定义的常量来引用。

#### 示例：
```java
public class EnumExample {
    public static void main(String[] args) {
        Day today = Day.MONDAY;  // 使用枚举常量

        // 输出枚举值
        System.out.println("Today is: " + today);

        // 使用枚举常量进行条件判断
        switch (today) {
            case MONDAY:
                System.out.println("Start of the work week.");
                break;
            case FRIDAY:
                System.out.println("It's almost weekend!");
                break;
            default:
                System.out.println("Another day.");
        }
    }
}
```

### 3. **枚举的属性和方法**
枚举不仅仅是常量的集合，还可以包含属性、构造方法和方法。你可以为枚举定义字段、构造函数和自定义方法，以使它们更具表现力。

#### 示例：带字段和构造方法的枚举
```java
public enum Day {
    MONDAY("Start of work week"),
    TUESDAY("Second day of work week"),
    WEDNESDAY("Mid-week"),
    THURSDAY("Almost there"),
    FRIDAY("End of work week"),
    SATURDAY("Weekend!"),
    SUNDAY("Weekend!");

    private String description;  // 每个枚举常量的描述

    // 构造函数
    Day(String description) {
        this.description = description;
    }

    // 获取描述
    public String getDescription() {
        return description;
    }
}

public class EnumWithFieldsExample {
    public static void main(String[] args) {
        // 输出每个枚举常量及其描述
        for (Day day : Day.values()) {
            System.out.println(day + ": " + day.getDescription());
        }
    }
}
```

#### 解释：
- 每个枚举常量都具有一个 `description` 字段。
- 枚举类型 `Day` 通过构造方法初始化每个常量的描述。
- `values()` 方法是自动生成的，它返回一个包含所有枚举常量的数组。
- `getDescription()` 方法用于获取每个枚举常量的描述。

### 4. **枚举的常用方法**
Java 枚举提供了许多有用的方法，以下是一些常用的方法：

- **`values()`**：返回枚举类中所有常量的数组。
- **`valueOf(String name)`**：根据常量名返回相应的枚举常量。它会抛出 `IllegalArgumentException` 如果提供的名字不匹配任何常量。
- **`ordinal()`**：返回枚举常量的序数（即它在枚举定义中的位置，从0开始）。
- **`name()`**：返回枚举常量的名称。

#### 示例：
```java
public class EnumMethodsExample {
    public static void main(String[] args) {
        // 获取所有枚举常量
        Day[] days = Day.values();
        for (Day day : days) {
            System.out.println(day);
        }

        // 获取枚举常量的名称
        Day friday = Day.FRIDAY;
        System.out.println("Enum name: " + friday.name());

        // 获取枚举常量的序数
        System.out.println("Ordinal of Friday: " + friday.ordinal());

        // 根据字符串获取枚举常量
        Day dayFromString = Day.valueOf("MONDAY");
        System.out.println("Day from string: " + dayFromString);
    }
}
```

### 5. **枚举与接口**
枚举可以实现接口，从而使枚举类型具备更复杂的行为。

#### 示例：枚举实现接口
```java
public interface Describable {
    String getDescription();
}

public enum Day implements Describable {
    MONDAY("Start of work week"),
    TUESDAY("Second day of work week"),
    WEDNESDAY("Mid-week"),
    THURSDAY("Almost there"),
    FRIDAY("End of work week"),
    SATURDAY("Weekend!"),
    SUNDAY("Weekend!");

    private String description;

    Day(String description) {
        this.description = description;
    }

    @Override
    public String getDescription() {
        return description;
    }
}

public class EnumWithInterfaceExample {
    public static void main(String[] args) {
        for (Day day : Day.values()) {
            System.out.println(day + ": " + day.getDescription());
        }
    }
}
```

### 6. **枚举的比较**
Java 枚举类实现了 `Comparable` 接口，因此可以进行自然排序。枚举常量按它们在定义时的顺序排序。

```java
public enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

public class EnumCompareExample {
    public static void main(String[] args) {
        Day day1 = Day.MONDAY;
        Day day2 = Day.FRIDAY;

        if (day1.ordinal() < day2.ordinal()) {
            System.out.println(day1 + " comes before " + day2);
        } else {
            System.out.println(day1 + " comes after or is the same as " + day2);
        }
    }
}
```
