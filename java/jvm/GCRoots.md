# GC Roots

在HotSpot虚拟机（JVM）中，**GC Root（垃圾回收根对象）** 是垃圾回收（GC）过程中用来作为追踪存活对象的起点。GC使用可达性分析（Reachability Analysis）来确定对象是否存活，而GC Root就是这个分析的起始点。

## **GC Root的主要来源**

HotSpot JVM中的GC Root主要包括以下几类对象：

1. **Java栈中的引用（栈帧中的局部变量表）**  
   - 任何线程正在执行的方法中的局部变量（`LocalVariableTable`）引用的对象。
   - 这些变量存放在栈帧的操作数栈和局部变量表中。

2. **方法区中的静态变量**  
   - 被类加载器加载的类的静态变量（`static`），如果它们引用了对象，那么这些对象就是GC Root。

3. **方法区中的常量**  
   - 运行时常量池中的对象，比如`String Table`中的字符串（常驻字符串）。

4. **JNI（Native Interface）引用的对象**  
   - 例如C/C++代码通过JNI接口创建的`global reference`（全局引用）。

5. **被同步锁（Monitor）持有的对象**  
   - 被`sychronized`关键字或`ReentrantLock`等同步工具锁定的对象，也会被视为GC Root。

6. **JVM内部的特殊对象**  
   - 一些特殊用途的对象，例如用于GC自身管理的对象（如`Finalizer`对象）。

### **GC Root的作用**
GC算法（如标记-清除、标记-整理、复制算法、G1等）会以GC Root为起点，递归遍历所有可达的对象，并标记为存活对象。未被标记的对象会被认为是垃圾，并在GC阶段被回收。
