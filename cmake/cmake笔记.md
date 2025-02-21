# cmake 笔记



## 一、环境



```bash
# OS
ubuntu 22.04.3 LTS

# cmake --version
cmake version 3.16.3
CMake suite maintained and supported by Kitware (kitware.com/cmake).

# gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/9/lto-wrapper
OFFLOAD_TARGET_NAMES=nvptx-none:hsa
OFFLOAD_TARGET_DEFAULT=1
Target: x86_64-linux-gnu
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 9.4.0-1ubuntu1~20.04.1' --with-bugurl=file:///usr/share/doc/gcc-9/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++,gm2 --prefix=/usr --with-gcc-major-version-only --program-suffix=-9 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --enable-default-pie --with-system-zlib --with-target-system-zlib=auto --enable-objc-gc=auto --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none=/build/gcc-9-Av3uEd/gcc-9-9.4.0/debian/tmp-nvptx/usr,hsa --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu
Thread model: posix
gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.1) 

# make -v
GNU Make 4.2.1
Built for x86_64-pc-linux-gnu
Copyright (C) 1988-2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

```



## 二、使用



### 1、注释



```cmake
# 单行注释

#[[
# 号后不要有空格
多行注释
111
]]

project(hello)
```



### 2、日志



**备注**

- (无): 重要消息
- STATUS: 非重要消息，stdout
- WARNING: 警告，会继续执行，stderr
- AUTHOR_WARNING: 会继续执行，stderr
- SEND_ERROR: 继续执行，会跳过生成的步骤, stderr
- FATAL_ERROR: 终止所有处理过程, stderr

```cmake
# 格式
# message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] "message to display" ...)
message("default message")
message(STATUS "status message")
message(WARNING "warning message")
message(AUTHOR_WARNING "author warning message")
message(SEND_ERROR "send error message")
message(FATAL_ERROR "fatal error")
message("default message")

# 结果
[STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR]message to display...
default message
-- status message
CMake Warning at CMakeLists.txt:13 (message):
  warning message


CMake Warning (dev) at CMakeLists.txt:14 (message):
  author warning message
This warning is for project developers.  Use -Wno-dev to suppress it.

CMake Error at CMakeLists.txt:15 (message):
  send error message


CMake Error at CMakeLists.txt:16 (message):
  fatal error


-- Configuring incomplete, errors occurred!
See also "/home/hello/code/cmake/hello/build/CMakeFiles/CMakeOutput.log".
```







### 3、限制cmake最低版本



```cmake
cmake_minimum_required(VERSION 3.20.0)
```



### 4、定义工程信息

```cmake
# PROJECT 指令的语法是：
project(<PROJECT-NAME> [<language-name>...])
project(<PROJECT-NAME>
       [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
       [DESCRIPTION <project-description-string>]
       [HOMEPAGE_URL <url-string>]
       [LANGUAGES <language-name>...])
       
#
cmake_minimum_required(VERSION 3.16.0)
if (POLICY CMP0048)
  cmake_policy(SET CMP0048 NEW)
endif (POLICY CMP0048)
project(
    hello
    VERSION 1.0.0
    LANGUAGES CXX
    HOMEPAGE_URL "https://hello.com"
)
```



### 5、定义变量



```cmake
cmake_minimum_required(VERSION 3.16.0)
project(hello)

# 空格分割
set(K1 V1 V2 V3)

# K1 值被覆盖
set(K1 V4)

# 分号分割
set(K2 H1;H2;H3)

set(K3 ${K2} H4 H5)
# c++标准
# 运行时指定 cmake .. -DCMAKE_CXX_STANDARD=11
set(CMAKE_CXX_STANDARD 11)

message(${K1})
message(${K2})
message(${K3})
message(${CMAKE_CXX_STANDARD})
```



### 6、List



```cmake
cmake_minimum_required(VERSION 3.0.0)
project(hello)
set(k1 v1 v2 v3)
message("k1: " ${k1})

# 追加
list(APPEND k1 v4 v5 v4)
message("k1: " ${k1})

# 移除
list(REMOVE_ITEM k1 v1 v4)
message("k1: " ${k1})

# 长度
list(LENGTH k1 length)
message(${length})

# 使用索引
list(GET k1 0 -1 -2 k2)
message("k2: " ${k2})

# 连接字符串
list(JOIN k1 , k3)
message("k3: " ${k3})

# 查找元素，不存在返回-1
list(FIND k1 v1 k4)
message("k4: " ${k4})

list(FIND k1 v3 k4)
message("k4: " ${k4})

list(APPEND k1 v3 v3)
message("k1: " ${k1})

# 返回第一个匹配的元素
list(FIND k1 v3 k4)
message("k4: " ${k4})

# 插入到指定位置
set(K5 v1 v2 v3)
list(INSERT K5 1 v4 v5)
message("K5: " ${K5})

# 将元素插入到0位置
set(K6 v1 v2 v3)
list(PREPEND K6 v4 v5)
message("K6: " ${K6})

# 移除最后一个
set(K7 v1 v2 v3)
list(POP_BACK K7 K8)
message("K7: " ${K7})
message("K8: " ${K8})

# 移除第一个
set(K9 v1 v2 v3)
list(POP_FRONT K9 K10)
message("K9: " ${K9})
message("K10: " ${K10})

# 移除指定元素
set(K11 v1 v2 v3)
list(REMOVE_AT K11 1)
message("K11: " ${K11})

# 移除重复元素
set(K12 v1 v2 v3 v2 v4 v6 v4)
list(REMOVE_DUPLICATES K12)
message("K12: " ${K12})

# 翻转
set(K13 v1 v2 v3)
list(REVERSE K13)
message("K13: " ${K13})

#[[
排序
list (SORT <list> [COMPARE <compare>] [CASE <case>] [ORDER <order>])
COMPARE：指定排序方法。有如下几种值可选：
    - STRING:按照字母顺序进行排序，为默认的排序方法
    - FILE_BASENAME：如果是一系列路径名，会使用basename进行排序
    - NATURAL：使用自然数顺序排序
CASE：指明是否大小写敏感。有如下几种值可选：
    - SENSITIVE: 按照大小写敏感的方式进行排序，为默认值
    - INSENSITIVE：按照大小写不敏感方式进行排序
ORDER：指明排序的顺序。有如下几种值可选：
    - ASCENDING:按照升序排列，为默认值
    - DESCENDING：按照降序排列
]]


# 降序
set(K14 v1 v2 v3 v4)
list(
    SORT
    K14
    COMPARE STRING
    CASE SENSITIVE
    ORDER DESCENDING)
message("K14: " ${K14})

## out
k1: v1v2v3
k1: v1v2v3v4v5v4
k1: v2v3v5
3
k2: v2v5v3
k3: v2,v3,v5
k4: -1
k4: 1
k1: v2v3v5v3v3
k4: 1
K5: v1v4v5v2v3
K6: v4v5v1v2v3
K7: v1v2
K8: v3
K9: v2v3
K10: v1
K11: v1v3
K12: v1v2v3v4v6
K13: v3v2v1
K14: v4v3v2v1
-- Configuring done
-- Generating done
-- Build files have been written to: /home/hello/code/cmake/hello/build
```



### 7、预定义宏



| 宏                       | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| PROJECT_SOURCE_DIR       | CMakeLists.txt所在位置                                       |
| PROJECT_BINARY_DIR       | 执行cmake 命令路径                                           |
| CMAKE_CURRENT_SOURCE_DIR | 当前处理的CMakeLists.txt所在位置                             |
| CMAKE_CURRENT_BINARY_DIR | target 编译目录                                              |
| EXECUTABLE_OUTPUT_PATH   | 目标二进制可执行文件的存放位置, 需使用set 命令指定           |
| LIBRARY_OUTPUT_PATH      | 目标链接库文件的存放位置, 需使用set 命令指定                 |
| PROJECT_NAME             | 工程名称, project(hello) 指定的名称                          |
| CMAKE_BINARY_DIR         | 项目实际构建路径，假设在build目录进行的构建，那么得到的就是这个目录的路径 |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |
|                          |                                                              |



```cmake
cmake_minimum_required(VERSION 3.16.0)
project(hello)

# camke ..
# CMakeLists位置: /home/hello/code/cmake/hello/CMakeLists.txt
# /home/hello/code/cmake/hello/build

# CMakeLists.txt所在位置
message("PROJECT_SOURCE_DIR: " ${PROJECT_SOURCE_DIR})

# 执行cmake 命令路径
message("PROJECT_BINARY_DIR: " ${PROJECT_BINARY_DIR})

# 当前处理的CMakeLists.txt所在位置
message("CMAKE_CURRENT_SOURCE_DIR: " ${CMAKE_CURRENT_SOURCE_DIR})

# target 编译目录
message("CMAKE_CURRENT_BINARY_DIR: " ${CMAKE_CURRENT_BINARY_DIR})

# 目标二进制可执行文件的存放位置, 需使用set 命令指定
message("EXECUTABLE_OUTPUT_PATH: " ${EXECUTABLE_OUTPUT_PATH})

# 目标链接库文件的存放位置, 需使用set 命令指定
message("LIBRARY_OUTPUT_PATH: " ${LIBRARY_OUTPUT_PATH})

# 工程名称, project(hello) 指定的名称
message("PROJECT_NAME: " ${PROJECT_NAME})

# 项目实际构建路径，假设在build目录进行的构建，那么得到的就是这个目录的路径
message("CMAKE_BINARY_DIR: " ${CMAKE_BINARY_DIR})


# out

PROJECT_SOURCE_DIR: /home/hello/code/cmake/hello
PROJECT_BINARY_DIR: /home/hello/code/cmake/hello/build
CMAKE_CURRENT_SOURCE_DIR: /home/hello/code/cmake/hello
CMAKE_CURRENT_BINARY_DIR: /home/hello/code/cmake/hello/build
EXECUTABLE_OUTPUT_PATH: 
LIBRARY_OUTPUT_PATH: 
PROJECT_NAME: hello
CMAKE_BINARY_DIR: /home/hello/code/cmake/hello/build
-- Configuring done
-- Generating done
-- Build files have been written to: /home/hello/code/cmake/hello/build

```



### 8、定义宏



**test.c**

```c
#include<stdio.h>

int main()
{
#ifdef HELLO
    printf("hello\n");
#endif
    printf("world\n");
}
```



**CMakeLists.txt**

```cmake
cmake_minimum_required(VERSION 3.16.0)
project(hello)

# 定义宏
# 类似 gcc -DHELLO test.c -o test
add_definitions(-DHELLO)
add_executable(test test.c)
```

**Out**

```
hello
world
```



### 9、搜索文件



```cmake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.16.0)
project(calc)

aux_source_directory(${PROJECT_SOURCE_DIR}/lib SRC_LIST1)

# GLOB
file(GLOB SRC_LIST2 "${PROJECT_SOURCE_DIR}/lib/*.c")

# GLOB_RECURSE  递归搜索
file(GLOB_RECURSE SRC_LIST3 "${PROJECT_SOURCE_DIR}/lib/*c")

message("SRC_LIST1: " ${SRC_LIST1})
message("SRC_LIST2: " ${SRC_LIST2})
message("SRC_LIST3: " ${SRC_LIST3})

# out
SRC_LIST1: /home/hello/code/cmake/hello/lib/add.c/home/hello/code/cmake/hello/lib/div.c/home/hello/code/cmake/hello/lib/mult.c/home/hello/code/cmake/hello/lib/sub.c
SRC_LIST2: /home/hello/code/cmake/hello/lib/add.c/home/hello/code/cmake/hello/lib/div.c/home/hello/code/cmake/hello/lib/mult.c/home/hello/code/cmake/hello/lib/sub.c
SRC_LIST3: /home/hello/code/cmake/hello/lib/add.c/home/hello/code/cmake/hello/lib/div.c/home/hello/code/cmake/hello/lib/mult.c/home/hello/code/cmake/hello/lib/sub.c
-- Configuring done
-- Generating done
-- Build files have been written to: /home/hello/code/cmake/hello/build
```



### 10、包含头文件

```cmake
include_directories(path)
```



### 11、生成可执行文件

**源文件**

```c
# calc.h
#ifndef _CALC_H
#define _CALC_H

int add(int a, int b);
double div(int a, int b);
int mult(int a, int b);
int sub(int a, int b);
#endif


# add.c
int add(int a, int b)
{
    return a + b;
}

# div.c
double div(int a, int b)
{
    return a / b;
}

# sub.c
int sub(int a, int b)
{
    return a - b;
}

# mult.c
int mult(int a, int b)
{
    return a * b;
}

```



```cmake
# tree
├── CMakeLists.txt
├── include
│   └── calc.h
├── lib
│   ├── add.c
│   ├── div.c
│   ├── mult.c
│   └── sub.c
└── main.c

# CMakeLists.txt
cmake_minimum_required(VERSION 3.16.0)
project(calc)
include_directories(${PROJECT_SOURCE_DIR}/include)

file(
    GLOB
    SRC_LIST
    ${PROJECT_SOURCE_DIR}/lib/*.c
    ${PROJECT_SOURCE_DIR}/*.c
)
add_executable(clac ${SRC_LIST})
```



### 12、静态库



```cmake
cmake_minimum_required(VERSION 3.16.0)
project(calc)
include_directories(${PROJECT_SOURCE_DIR}/include)

file(
    GLOB
    LIB_SRC_LIST
    ${PROJECT_SOURCE_DIR}/lib/*.c
)
# 默认静态库
# add_library(calc ${LIB_SRC_LIST})
add_library(calc STATIC ${LIB_SRC_LIST})

# ls -l
-rw-rw-r-- 1 hello hello  14K Oct 22 16:03 CMakeCache.txt
drwxrwxr-x 5 hello hello 4.0K Oct 22 16:03 CMakeFiles
-rw-rw-r-- 1 hello hello 1.5K Oct 22 16:03 cmake_install.cmake
-rw-rw-r-- 1 hello hello 5.8K Oct 22 16:03 libcalc.a
-rw-rw-r-- 1 hello hello 6.6K Oct 22 16:03 Makefile
```



### 13、动态库

```cmake
cmake_minimum_required(VERSION 3.16.0)
project(calc)
include_directories(${PROJECT_SOURCE_DIR}/include)

file(
    GLOB
    LIB_SRC_LIST
    ${PROJECT_SOURCE_DIR}/lib/*.c
)
# 生成动态库
add_library(calc SHARED ${LIB_SRC_LIST})

# ls -l
-rw-rw-r-- 1 hello hello  14K Oct 22 16:02 CMakeCache.txt
drwxrwxr-x 5 hello hello 4.0K Oct 22 16:02 CMakeFiles
-rw-rw-r-- 1 hello hello 1.5K Oct 22 16:02 cmake_install.cmake
-rwxrwxr-x 1 hello hello  16K Oct 22 16:02 libcalc.so
-rw-rw-r-- 1 hello hello 6.6K Oct 22 16:02 Makefile
```



### 14、链接库

```cmake
cmake_minimum_required(VERSION 3.16.0)
project(calc)
include_directories(${PROJECT_SOURCE_DIR}/include)

file(
    GLOB
    LIB_SRC_LIST
    ${PROJECT_SOURCE_DIR}/lib/*.c
)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
add_library(calc SHARED ${LIB_SRC_LIST})
# 库路径
# link_directories(${PROJECT_SOURCE_DIR}/bin)

# 链接库
# link_libraries(calc)

# 生成可执行文件
add_executable(app main.c)

# 链接库
target_link_libraries(app calc)
```





## 三、相关资料



https://subingwen.cn/cmake/CMake-primer/

https://cmake.org/documentation/

