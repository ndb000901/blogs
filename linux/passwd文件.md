# passwd文件



## 1、环境

- ubuntu 20.04



## 2、/etc/passwd 结构



```
登录名:加密口令(已转移到其他文件，/etc/shadow):数字用户ID:数字组ID:注释字段:起始目录:shell程序
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

```



## 3、源码



```c
#include<pwd.h>
#include<stdio.h>

void test()
{
    struct passwd *ptr;

    setpwent(); // 指向/etc/passwd文件开头
    while((ptr = getpwent()) != NULL)
    {
        printf(
            "%s:%s:%d:%d:%s:%s:%s\n",
            ptr->pw_name,
            ptr->pw_passwd,
            ptr->pw_uid,
            ptr->pw_gid,
            ptr->pw_gecos,
            ptr->pw_dir,
            ptr->pw_shell
        );
    }
    endpwent();// 关闭打开的文件
}


```



