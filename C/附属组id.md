# passwd文件



## 1、环境

- ubuntu 20.04



## 2、源码



```c
#include<stdio.h>
#include<unistd.h>

void test()
{
    __gid_t list[10];
    int nums;
    nums = getgroups(10, list);

    for (int i = 0; i < nums; i++) {
        printf("%d--> %d\n", i, list[i]);
    }
}

```



