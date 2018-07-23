# os summary
秋招复习，对操作系统常问的问题总结。

<!-- GFM-TOC -->
* [进程与线程的区别](#进程与线程的区别)
* [进程之间的通信](#进程之间的通信)
<!-- GFM-TOC -->

## 进程与线程的区别
### 1. 进程
进程是一个执行中程序的实例，系统中每个程序都是运行在某个进程的上下文中的，上下文是由所需状态组成，包括：程序的代码和数据、堆及用户栈、环境变量、打开文件描述符等。是系统进行资源分配和调度的一个独立单位。

### 2. 线程
线程是独立调度的基本单位。一个进程中可以有多个线程，至少有一个线程，它们共享进程资源。

### 3. 区别
- 调度：线程是调度资源的基本单位。在同一进程中，线程的切换不会引起进程切换，从一个进程内的线程切换到另一个进程中的线程时，会引起进程切换。

- 拥有资源：进程是拥有资源的基本单位，线程不拥有资源，线程可以访问隶属进程的资源，包括：程序的代码和数据、堆及用户栈、环境变量、打开文件描述符等。

- 系统开销：由于创建或撤销进程时，系统都要为之分配或回收资源，如内存空间、I/O 设备等，所付出的开销远大于创建或撤销线程时的开销。类似地，在进行进程切换时，涉及当前执行进程 CPU 环境的保存及新调度进程 CPU 环境的设置，而线程切换时只需保存和设置少量寄存器内容，开销很小。

- 通信方面：进程间通信 (IPC) 需要进程同步和互斥手段的辅助，以保证数据的一致性。而线程间可以通过直接读/写同一进程中的数据段（如全局变量）来进行通信。

举例：QQ 和浏览器是两个进程，浏览器进程里面有很多线程，例如 HTTP 请求线程、事件响应线程、渲染线程等等，线程的并发执行使得在浏览器中点击一个新链接从而发起 HTTP 请求时，浏览器还可以响应用户的其它事件。

## 进程之间的通信
<div align="center"> <img src="./imgs/ipc.jpg"/> </div><br>

### 1. 管道
用于连接一个读进程和一个写进程以实现以它们之间通信的一个共享文件，又名pipe文件。

特点：
* 它们是半双工的（即数据只能在一个方向上流动）
* 一般在父子进程中使用，一个管道由一个进程创建，接着调用fork，此后父子进程间就可应用管道通信。

函数：
~~~c
# include <unistd.h>
int pipe(int fileds[2]);
~~~

例子：
~~~c
int
main(void)
{
    int n;
    int fd[2];
    pid_t pid;
    char line[MAXLINE];

    if (pipe[fd] < 0)
        err_sys("pipe error");
    if ((pid = fork()) < 0) {
        err_sys("fork error");
    } else if (pid > 0) {
        close(fd[0]);
        write(fd[1], "hello world\n", 12);
    } else {
        close(fd[1]);
        n = read(fd[0], line, MAXLINE);
        write(STDOUT_FILENO, line, n);
    }

exit(0);
}
~~~
管道是通过调用 pipe 函数创建的，fd[0] 用于读，fd[1] 用于写。

<div align="center"> <img src="./imgs/pipe1.jpg"/> </div><br>
<div align="center"> <img src="./imgs/pipe2.jpg"/> </div><br>

### 2. FIFO
FIFO是一种文件类型，也称为命名管道，一般的文件I/O函数都可应用于FIFO。

特点：
去除了管道只能在父子进程中使用的限制，不相关的进程也能交换数据。

函数：
~~~c
#include <sys/stat.h>
int mkfifo(const char *path, mode_t mode);
~~~

<div align="center"> <img src="./imgs/FIFO.jpg"/> </div><br>



参考：

https://github.com/CyC2018/Interview-Notebook/edit/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.md
《计算机操作系统》
《深入理解计算机系统》
