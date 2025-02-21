# passwd文件



## 1、环境

- ubuntu 20.04



## 2、/etc/shadow 结构



```
用户名:密码:上次修改密码日期:密码过期期限:密码更改提醒期限:密码失效期限:账户失效期限:保留字段
user1:$6$D7rG5Xsh$VrBsEK.Wz6twmuBlMNHsGOq3B2JiO0tMTc5f5S6qSDuJFeqNOt3VpDQwhd1iXcimYlBnK.l4lXNoPfFF9aQ7d.:18930:0:99999:7:::
user2:$6$VYI6X/A2$ZnSwpGz13XWEM4vS5I0SiUDUzIZBa.7w9WlHyG2mGg9bOz0BzgcqsCAl3plQUDy1Lwe2Nz25SXtU.VYBd7W//.:18930:0:99999:7:::


```



## 3、源码



```c
#include<stdio.h>
#include<shadow.h>

void test()
{
    struct spwd *ptr;

    setspent();

    while((ptr = getspent()) != NULL)
    {
        printf(
            "%s:%s:%ld:%ld:%ld:%ld:%ld:%ld:%ld\n",
            ptr->sp_namp,
            ptr->sp_pwdp,
            ptr->sp_lstchg,
            ptr->sp_min,
            ptr->sp_max,
            ptr->sp_warn,
            ptr->sp_inact,
            ptr->sp_expire,
            ptr->sp_flag
        );
    }

    endspent();
}
```



