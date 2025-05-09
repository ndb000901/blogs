# Stack

`Stack` 是 Java 中提供的**后进先出（LIFO）**  的栈结构类，定义在 `java.util` 包中。

```java
public class Stack<E> extends Vector<E>
```

### 核心特性：

* 继承自 `Vector`，**线程安全**
* 遵循 **LIFO（Last In First Out）**
* 方法语义清晰（push/pop/peek）
* 老牌同步栈结构，**不推荐新项目使用**

---

## 1. 类定义与继承体系

```java
public class Stack<E> extends Vector<E>
```

* **继承自 Vector** → 所有方法默认是同步的。
* 支持泛型。
* 不推荐用于现代高并发环境。

### UML 简图：

```
Object
  └── AbstractCollection
        └── AbstractList
              └── Vector
                    └── Stack
```

---

## 2. 核心方法

### 2.1 `push(E item)` 入栈

```java
public E push(E item) {
    addElement(item); // 实际调用的是 Vector 的 addElement
    return item;
}
```

底层即调用：

```java
public synchronized void addElement(E obj) {
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = obj;
}
```

### 2.2 `pop()` 出栈

```java
public synchronized E pop() {
    E obj;
    int len = size();
    obj = peek();
    removeElementAt(len - 1);
    return obj;
}
```

* **抛出 `EmptyStackException`**：如果栈为空。
* 实质上调用的是 `Vector` 的 `removeElementAt`。

### 2.3 `peek()` 查看栈顶元素

```java
public synchronized E peek() {
    int len = size();
    if (len == 0)
        throw new EmptyStackException();
    return elementAt(len - 1);
}
```

### 2.4 `empty()` 判断是否为空

```java
public boolean empty() {
    return size() == 0;
}
```

### 2.5 `search(Object o)` 从栈顶查找元素的位置

```java
public synchronized int search(Object o) {
    int i = lastIndexOf(o);
    if (i >= 0) {
        return size() - i;
    }
    return -1;
}
```

---

## 3. 线程安全机制

由于继承自 `Vector`，`Stack` 所有方法默认 `synchronized`，属于**粗粒度同步**。

优点：

* 多线程安全。

缺点：

* **并发性能极差**，不适合高并发。
* 所有操作都加锁，导致阻塞严重。

---

## 4. `Stack` VS `Deque`（现代推荐）

| 特性   | `Stack`（老版）  | `Deque`（如 `ArrayDeque`） |
| ---- | ------------ | -------------------- |
| 线程安全 | （但性能差）       | （非线程安全，需加锁）          |
| 性能   | 差（synchronized） | 高（无锁）                |
| 推荐程度 | 不推荐          | 推荐用 `ArrayDeque` 代替  |
| 方法语义 | LIFO（push/pop） | 双端操作（支持 LIFO 和 FIFO） |

**推荐使用：**

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
System.out.println(stack.pop()); // 2
```

---


