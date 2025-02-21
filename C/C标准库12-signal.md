# C标准库12-signal

`signal.h` 是 C 语言标准库中的一个头文件，**用于定义信号（Signal）相关的函数、常量和数据类型**。  
信号是一种**进程间通信（IPC）**和**异常处理**机制，**主要用于通知进程发生了特定的事件，如终止、中断、浮点异常等**。

---

## **1. `signal.h` 的作用**
- **进程控制**：可以拦截和处理操作系统发送的信号（如 `SIGINT`、`SIGTERM`）。
- **异常处理**：捕获如除零错误（`SIGFPE`）或段错误（`SIGSEGV`）。
- **进程间通信（IPC）**：一个进程可以向另一个进程发送信号（如 `kill()` 发送 `SIGUSR1`）。
- **强制终止进程**：使用 `SIGKILL` 或 `SIGTERM` 终止进程。

---

## **2. `signal.h` 的核心组件**
### **2.1 信号编号（Signal Numbers）**
`signal.h` 定义了一系列标准信号，**不同系统的信号编号可能不同**，但名称是统一的。

| 信号名称 | 数值（Linux） | 说明 |
|----------|-------------|------|
| `SIGABRT` | 6  | 进程调用 `abort()` 终止 |
| `SIGALRM` | 14 | `alarm()` 计时器超时 |
| `SIGBUS`  | 7  | 硬件故障，如未对齐访问 |
| `SIGCHLD` | 17 | 子进程终止通知父进程 |
| `SIGCONT` | 18 | 继续执行被暂停的进程 |
| `SIGFPE`  | 8  | 数学错误，如除零 |
| `SIGHUP`  | 1  | 终端挂起或控制进程终止 |
| `SIGILL`  | 4  | 非法指令 |
| `SIGINT`  | 2  | 用户（Ctrl+C）中断 |
| `SIGKILL` | 9  | 杀死进程（无法捕获） |
| `SIGPIPE` | 13 | 管道破裂（写入无读者的管道） |
| `SIGQUIT` | 3  | 用户（Ctrl+\）退出 |
| `SIGSEGV` | 11 | 段错误（访问非法内存） |
| `SIGSTOP` | 19 | 停止进程（无法捕获） |
| `SIGTERM` | 15 | 终止进程（默认 `kill` 发送） |
| `SIGTSTP` | 20 | 用户（Ctrl+Z）暂停进程 |
| `SIGUSR1` | 10 | 用户自定义信号 1 |
| `SIGUSR2` | 12 | 用户自定义信号 2 |

---

## **3. `signal.h` 的主要函数**
### **3.1 `signal()`**
`signal()` 用于**设置信号处理函数**：
```c
void (*signal(int signum, void (*handler)(int)))(int);
```
- `signum`：信号编号（如 `SIGINT`）。
- `handler`：处理函数，可以是：
  - **`SIG_DFL`**：使用默认处理方式。
  - **`SIG_IGN`**：忽略该信号。
  - **自定义函数**：用户定义的信号处理函数。

**示例：捕获 `SIGINT`（Ctrl+C）并执行自定义处理**
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void handler(int signum) {
    printf("收到信号 %d，忽略 Ctrl+C\n", signum);
}

int main() {
    signal(SIGINT, handler);  // 捕获 Ctrl+C
    while (1) {
        printf("运行中... 按 Ctrl+C 试试\n");
        sleep(1);
    }
    return 0;
}
```
**执行后按 `Ctrl+C`，会看到**
```
运行中... 按 Ctrl+C 试试
运行中... 按 Ctrl+C 试试
收到信号 2，忽略 Ctrl+C
运行中... 按 Ctrl+C 试试
```

---

### **3.2 `raise()`**
`raise()` 用于**向自己发送信号**：
```c
int raise(int signum);
```
**示例：触发 `SIGTERM` 并终止进程**
```c
#include <stdio.h>
#include <signal.h>

int main() {
    printf("发送 SIGTERM 终止进程\n");
    raise(SIGTERM);
    printf("不会执行到这里\n");
    return 0;
}
```
---

### **3.3 `kill()`**
`kill()` 用于**向指定进程发送信号**：
```c
int kill(pid_t pid, int signum);
```
- `pid > 0`：向 `pid` 指定的进程发送信号。
- `pid == 0`：向当前进程组的所有进程发送信号。
- `pid == -1`：向所有权限允许的进程发送信号。

**示例：向自己发送 `SIGUSR1`**
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void handler(int signum) {
    printf("收到 SIGUSR1 信号！\n");
}

int main() {
    signal(SIGUSR1, handler);
    printf("发送 SIGUSR1 信号\n");
    kill(getpid(), SIGUSR1);
    return 0;
}
```

---

### **3.4 `sigaction()`**
`sigaction()` 提供更强大的信号处理能力，**比 `signal()` 更安全**：
```c
int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
```
- `signum`：信号编号。
- `act`：新的信号处理方式。
- `oldact`：存储旧的信号处理方式（可以为 `NULL`）。

**示例：使用 `sigaction()` 处理 `SIGINT`**
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void handler(int signum) {
    printf("收到信号 %d\n", signum);
}

int main() {
    struct sigaction sa;
    sa.sa_handler = handler;   // 设置信号处理函数
    sigemptyset(&sa.sa_mask);  // 不屏蔽额外信号
    sa.sa_flags = 0;

    sigaction(SIGINT, &sa, NULL);  // 处理 SIGINT
    while (1) {
        printf("程序运行中...\n");
        sleep(1);
    }
    return 0;
}
```

---

### **3.5 `sigprocmask()`**
`sigprocmask()` **控制进程信号屏蔽**（阻塞/解除信号）：
```c
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
```
- `SIG_BLOCK`：屏蔽信号。
- `SIG_UNBLOCK`：解除信号屏蔽。
- `SIG_SETMASK`：设置信号屏蔽集。

**示例：屏蔽 `SIGINT`（Ctrl+C 不生效）**
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

int main() {
    sigset_t set;
    sigemptyset(&set);
    sigaddset(&set, SIGINT);

    sigprocmask(SIG_BLOCK, &set, NULL);  // 屏蔽 SIGINT
    printf("SIGINT 被屏蔽 5 秒...\n");
    sleep(5);
    sigprocmask(SIG_UNBLOCK, &set, NULL);  // 解除屏蔽
    printf("SIGINT 解除屏蔽\n");

    while (1) sleep(1);
    return 0;
}
```

---

## **4. `signal.h` 注意事项**
1. `signal()` 可能不是线程安全的，推荐 `sigaction()`。
2. `SIGKILL` 和 `SIGSTOP` **无法被捕获或忽略**。
3. `raise()` 仅能影响当前进程，`kill()` 可用于其他进程。
4. `sigprocmask()` **仅适用于单线程程序**，多线程请使用 `pthread_sigmask()`。

---

## **5. 总结**
- `signal()` **简单但不安全**，推荐 `sigaction()` 。
- `raise()` **自己触发信号**，`kill()` **给其他进程发信号**。
- `sigprocmask()` **屏蔽信号**，`SIGKILL` **无法屏蔽**。
- **信号处理是异步的，避免使用 `printf()` 处理信号！**