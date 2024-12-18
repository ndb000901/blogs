# passwd文件



## 1、环境

- ubuntu 20.04



## 2、/etc/group 结构



```
组名:加密口令(已不再使用):数字组ID:用户1,用户2
wheel:x:10:root,user1,user2
staff:x:20:user1,user3

```



## 3、源码



```c
#include<stdio.h>
#include<grp.h>

void test()
{
    struct group *ptr;

    setgrent(); // 将getgrent读取地址移动到/etc/group文件开头
    while((ptr = getgrent()) != NULL)
    {
        printf(
            "%s:%s:%d:%s\n",
            ptr->gr_name,
            ptr->gr_passwd,
            ptr->gr_gid,
            *(ptr->gr_mem)
        );
    }
    endgrent();// 关闭打开的文件
}
```



