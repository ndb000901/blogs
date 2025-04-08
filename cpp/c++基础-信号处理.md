# c++基础-信号处理

C++ 中的**信号处理（Signal Handling）** 是一种处理异步事件的机制，最早源自 C 语言，常用于处理诸如中断、终止、非法操作等 **Unix/Linux 信号**。

---

## 1. 信号

信号是一种操作系统用于通知进程某种事件发生的机制。

常见信号及其编号（来自 `<signal.h>`）：

| 信号名       | 描述                  | 默认行为     |
|--------------|-----------------------|--------------|
| `SIGINT`     | Ctrl + C 中断         | 终止         |
| `SIGTERM`    | kill 命令默认信号     | 终止         |
| `SIGKILL`    | 无法捕捉/阻塞         | 强制终止     |
| `SIGSEGV`    | 段错误，非法访问内存  | core dump    |
| `SIGABRT`    | 调用 abort() 触发     | core dump    |
| `SIGFPE`     | 数学错误（除以0）     | core dump    |
| `SIGALRM`    | 定时器信号             | 终止         |
| `SIGCHLD`    | 子进程终止             | 忽略         |

---

## 2. 信号处理函数（signal）

在 `<csignal>` 或 `<signal.h>` 中定义：

```cpp
#include <csignal>
#include <iostream>
using namespace std;

void signalHandler(int signum) {
    cout << "捕获信号: " << signum << endl;
    exit(signum); // 正常退出
}

int main() {
    signal(SIGINT, signalHandler); // 注册Ctrl+C处理
    while (1) {
        cout << "运行中..." << endl;
        sleep(1);
    }
}
```

运行后，按 Ctrl+C 会触发 `signalHandler`。

---

## 3. 使用 `signal` 的注意事项

- `signal(int signum, sighandler)` 用于注册处理器。
- 处理器原型：`void handler(int)`。
- 不是所有信号都能捕捉：`SIGKILL`, `SIGSTOP` 不行。
- 某些信号处理函数 **必须快速返回**，不能执行阻塞 I/O。
- 注册处理器后，新的处理器替换旧的。

---

## 4. 使用 `sigaction`（更强大的 signal 版本）

`sigaction` 是 POSIX 提供的增强版，推荐使用：

```cpp
#include <signal.h>
#include <iostream>
#include <unistd.h>

void handler(int sig) {
    std::cout << "Received signal: " << sig << std::endl;
}

int main() {
    struct sigaction sa;
    sa.sa_handler = handler;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;

    sigaction(SIGINT, &sa, nullptr); // 注册处理器

    while (1) {
        std::cout << "Running..." << std::endl;
        sleep(1);
    }
}
```

### 相比 signal 的优点：
- 可设置信号屏蔽集（mask）
- 可设定行为标志（如 `SA_RESTART`）
- 线程安全
- 更可靠、可移植性更好

---

## 5. 发送信号

可以用 `raise()` 或 `kill()` 触发信号：

```cpp
raise(SIGINT);   // 向自己发信号
kill(pid, SIGTERM); // 向进程 pid 发信号
```

---

## 6. 屏蔽信号（阻塞信号）

```cpp
sigset_t set;
sigemptyset(&set);
sigaddset(&set, SIGINT);

// 阻塞 SIGINT
sigprocmask(SIG_BLOCK, &set, nullptr);

// 恢复
sigprocmask(SIG_UNBLOCK, &set, nullptr);
```

适用于临界区保护：防止某些信号打断关键代码。

---


