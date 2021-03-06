# C语言指针

## 指针

1. 内存四区：

* 代码区
* 全局区，全局的常量字符串，变量等
* 栈区。系统自动开辟，自动释放，不大
* 堆区。动态开辟的内存

2. 地址

   int 4个字节，一个字节8位

   把内存以单个字节为单位，分开。对每一个字节编号，编号即为地址

   * 编号是连续的
   * 唯一的
   * 取地址运算符：& 

3. 首地址：一段内存空间中第一个存储单元的地址。一维数组的首地址是第一个元素，二维数组的首地址是第一个一维数组。

4. 指针变量：用来存放地址的变量，内存大小4B

   地址是一些编号，一种数据

   整数 int a;

   地址 指针变量

   * 指针变量的定义: int *p, p里存的是地址

   * 指针变量的赋值：int a; p=&a   //p指向a

   * 指针变量的引用：

     访问a:    1)printf(a)      2)printf(*p)   

     *：1）取值运算符返回某一个地址中的值   

     ​      2）在定义时表示是指针变量

   野指针：不能明确  解决-> int *p=NULL

   空指针：void*

   指针的运算：

   * +、-、++、--  ：指针偏移

     ```
     char *p       char 1     p+1->1B
     int *p1       int  4     p1+1->4B
     double *p2    double 8   p2+1->8B
     ```

     指针变量的加减，以指针指向的类型空间为单位偏移

## 一维数组和指针

1. 定义一个一维数组，**数组名是这个数组的首地址**

   ```
   int a[4]
   a指向a[0],a[0]是int，
   a指向a[0] int元素，a的类型是int*
   &a这个地址指向整个数组 &a的类型是 int(*)[5]
   ```

2. 访问数组元素

   * 下标法

   * 指针法

     ```
     int a[5]={1,2,3,4,5}
     int *p=a //p指向a[0]
     for(int i=0;i<5;i++){
     	printf(*(p+i))  //* + *的优先级更大
     	print(*p++)     //* ++ 优先级相同，从右向左
     	print(*(a+i))   //不能用*a++，a是一个指针常量，不能改变
     }
     ```

     

## 二维数组和指针

```
int a[3][4]={1,2,3,4,5,6,7,8,9,10,11,12}
```

a是这个二维数组的首地址，a的类型是 int(*)[4]  a+1  ->16bits

a[0]是一维数组的数组名，即一维数组的首地址，a[0]指向a[0][0 ,

a[0]的类型是int *   a[0]+1 ->4 bits

```
a  int(*)[4]
&a int(*)[3][4]
a[0] int*
a[0][0] int
```



访问m,n

```
a[m][n]
*(a[m]+n)
*(*(a+m)+n)
```



# 4 the abstraction: the process

The definition of a process, informally, is quite simple: it is a running program

## 4.1 进程

操作系统为正在运行的程序提供的抽象就是进程。

进程机器状态的组成部分

1. 内存，指令、进程读取和写入的数据存在于内存中。该进程可以访问的内存是该进程的一部分
2. 寄存器，

## 4.2 进程API

* 创建（create）：操作系统必须包含一些创建新进程的方法。在shell中键入命令或双击应用程序图标时，会调用操作系统来创建新进程，运行指定的程序。
* 销毁（destroy）：由于存在创建进程的接口，因此系统还提供了一个强制销毁进程的接口。当然，很多进程会在运行完成后自行退出。但是，如果它们不退出，用户可能希望终止它们，因此停止失控进程的接口非常有用。
* 等待（wait）：有时等待进程停止运行是有用的，因此经常提供某种等待接口。
* 其他控制（miscellaneous control）：除了杀死或等待进程外，有时还可能有其他控制。例如，大多数操作系统提供某种方法来暂停进程（停止运行一段时间），然后恢复（继续运行）。
* 状态（statu）：通常也有一些接口可以获得有关进程的状态信息，例如运行了多长时间，或者处于什么状态

## 4.3 进程创建

操作系统运行程序

1.  先将代码和所有静态数据load到内存中，加载到进程的地址空间中，![截屏2020-11-24 下午4.32.44](images/%E6%88%AA%E5%B1%8F2020-11-24%20%E4%B8%8B%E5%8D%884.32.44.png)

   惰性加载：仅在程序执行期间加载需要的代码和数据片段-->分页和交换

2. 为程序的stack分配内存，存放局部变量，函数参数和返回地址。

   也可能给heap分配一些内存，用于显式请求的动态分配数据。malloc()请求，free()释放。数据结构需要堆

3. 初试I/O，UNIX里，每个进程都有3个打开的文件描述符，用于标准输入、输出和错误。

4. 启动程序，在main处运行，OS将cpu的控制权转移到新创建的进程中。



## 4.4 进程状态

* 运行running：正在执行指令
* 就绪ready：已准备好运行，但并未运行
* 阻塞blocked：执行了某种操作，直到发生其他事件时才会准备运行。如发起I/O请求

![截屏2020-11-24 下午4.42.33](images/%E6%88%AA%E5%B1%8F2020-11-24%20%E4%B8%8B%E5%8D%884.42.33.png)

* 初试状态init
* 最终状态final:也称为僵尸状态，它允许其他进程（通常是父进程）检查进程的返回代码。完成后，父进程将进行最后一次调用（例如，wait()），以等待子进程的完成，并告诉操作系统它可以清理这个正在结束的进程的所有相关数据结构。

## 4.5 数据结构

操作系统有一些关键数据结构来跟踪各种相关信息。对于停止的进程，寄存器上下文将保存其寄存器的内容。当一个进程停止时，它的寄存器将被保存到这个内存位置。通过恢复这些寄存器（将它们的值放回实际的物理寄存器中），操作系统可以恢复运行该进程。

操作系统可能会为所有就绪的进程保留某种进程列表（process list）

# 6. CPU mechanisms

By time sharing the CPU in this manner, virtualization is achieved.

challenges

* Performance: how can we implement virtualization without adding excessive overhead to the system?
* Control:how can we run processes efficiently while retaining control over the CPU?

##  6.1 受限直接执行

The “direct execution” part of the idea is simple: just run the program directly on the CPU.![截屏2020-10-16 下午5.33.36](/Users/chenxu/Documents/GitHub/learning-OS/operation system/images/截屏2020-10-16 下午5.33.36.png)

## 6.2 受限制的操作 user/kernel

* running on the CPU introduces a problem: what if the process wishes to perform some kind of **restricted operation**, such as issuing an I/O request to a disk, or gaining access to more system resources such as CPU or memory?

  ​            -->    introduce  **user mode and kernel mode**, when running in user mode, a process can’t issue I/O requests.

  In kernel mode, code that runs can do what it likes, including privileged operations such as issuing I/O requests and executing all types of restricted instructions.



![截屏2020-10-16 下午5.34.09](/Users/chenxu/Documents/GitHub/learning-OS/operation system/images/截屏2020-10-16 下午5.34.09.png)





## 6.3 进程切换，重获cpu控制权

How can the operating system **regain control** of the CPU so that it can switch between processes?

* cooperative approach

  In this style, the OS trusts the processes of the system to behave reasonably. Processes that run for too long are assumed to periodically give up the CPU so that the OS can decide to run some other task. 

  Most processes, as it turns out, transfer control of the CPU to the OS quite frequently by making system calls, include a  **yield** system call to transfer control to the OS.

  Or they do something illegal --> generate a **trap** to the os, then os has control again.

  **in a cooperative scheduling system, the OS regains control of the CPU by waiting for a system call or an illegal operation of some kind to take place**

* Non-cooperative approach

  **a timer interrupt**:when the interrupt is raised, the currently running process is halted, and a pre-configured **interrupt handler** in the OS runs.

whether to continue running the currently-running process, or switch to a different one. This decision is made by a part of the operating system known as the **scheduler**

if switch  ---> os execute a low_level piece of code(**context switch**)

all the OS has to do is save a few register values for the currently-executing process (onto its kernel stack, for example) and restore a few for the soon-to-be-executing process (from its kernel stack).![截屏2020-10-18 上午11.09.31](/Users/chenxu/Documents/GitHub/learning-OS/operation system/images/截屏2020-10-18 上午11.09.31.png)

Process A is running and then is interrupted by the timer interrupt. The hardware saves its registers (onto its kernel stack) and enters the kernel (switching to kernel mode). In the timer interrupt handler, the OS decides to switch from running Process A to Process B. At that point, it calls the switch() routine, which carefully saves current register values (into the process structure of A), restores the registers of Process B (from its process structure entry), and then switches contexts, specifically by changing the stack pointer to use B’s kernel stack (and not A’s). Finally, the OS returnsfrom-trap, which restores B’s registers and starts running it.





## summary

We have described some key low-level mechanisms to implement CPU virtualization, a set of techniques which we collectively refer to as **limited direct execution**. The basic idea is straightforward: just run the program you want to run on the CPU, but first make sure to set up the hardware so as to limit what the process can do without OS assistance.



In an analogous manner, the OS “baby proofs” the CPU, by first (during boot time) setting up the **trap handlers** and starting an **interrupt timer**, and then by only running processes in a **restricted mode**. By doing so, the OS can feel quite assured that processes can run efficiently, only requiring OS intervention to perform privileged operations or when they have monopolized the CPU for too long and thus need to be switched out.

## KEY CPU VIRTUALIZATION TERMS 

• The CPU should support at least two modes of execution: a restricted **user mode** and a privileged (non-restricted) **kernel mode**. 

• Typical user applications run in user mode, and use a **system call** to trap into the kernel to request operating system services.

 • The trap instruction saves register state carefully, changes the hardware status to kernel mode, and jumps into the OS to a pre-specified destination: the **trap table**.

 • When the OS finishes servicing a system call, it returns to the user program via another special **return-from-trap** instruction, which reduces privilege and returns control to the instruction after the trap that jumped into the OS.

 • The trap tables must be set up by the OS at boot time, and make sure that they cannot be readily modified by user programs. All of this is part of the **limited direct execution** protocol which runs programs efficiently but without loss of OS control. 

• Once a program is running, the OS must use hardware mechanisms to ensure the user program does not run forever, namely the timer interrupt. This approach is a **non-cooperative** approach to CPU scheduling. 

• Sometimes the OS, during a timer interrupt or system call, might wish to switch from running the current process to a different one, a low-level technique known as a **context switch**.



## questions

**What actions does a kernel perform to context switch between processes?**

1. In response to a clock interrupt, the OS saves the PC and user stack pointer
   of the currently executing process, and transfers control to the kernel clock
   interrupt handler,

2. The clock interrupt handler saves the rest of the registers, as well as other
machine state, such as the state of the floating point registers, in the process
PCB.
3. The OS invokes the scheduler to determine the next process to execute,
4. The OS then retrieves the state of the next process from its PCB, and restores
the registers. This restore operation takes the processor back to the state in
which this process was previously interrupted, executing in user code with
user mode privileges.

**What process state does a process enter immediately after it terminates? What is this state for, and when does it leave this state?**

Zombie state， it sends a SIGCHLD signal to the parent. It is up to the parent process to release the child process slot so that the parent can determine if the process exited successfully.

the parent releases the child process

