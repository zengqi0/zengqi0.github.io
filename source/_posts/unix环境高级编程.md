---
title: unix环境高级编程
date: 2021-04-10 14:54:25
categories:
 - 技术研究
---



# unix环境高级编程	

## 第三章 文 件 I / O

### 引言

本章所说明的函数经常被称之为不带缓存的I / O

不带缓存指的是每个 r e a d和w r i t e都调用内核中的一个系统调用

### 文件描述符

对于内核而言，所有打开文件都由文件描述符引用。文件描述符是一个非负整数。当打开一个现存文件或创建一个新文件时，内核向进程返回一个文件描述符。当读、写一个文件时，
用o p e n或c r e a t返回的文件描述符标识该文件，将其作为参数传送给r e a d或w r i t e。

### open函数

调用o p e n函数可以打开或创建一个文件

```
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int open(const char *path, int flags, [mode_t mode]);
//如果操作成功则返回一个文件描述符，否则返回-1；

返回：若成功为文件描述符，若出错为- 1
```

p a t h n a m e是要打开或创建的文件的名字

o f l a g参数可用来说明此函数的多个选择项

用下列一个或多个常数进行或运算构成o f l a g参数(这些常数定义在< f c n t l . h >头文件中)：

 O_RDONLY 只读打开。
• O_WRONLY 只写打开。
• O_RDWR 读、写打开。

以下的常量是选用的，这些选项是用来和上面的必选项进行按位或起来作为flags参数。

- O_APPEND：表示追加，如果原来文件里面有内容，则这次写入会写在文件的最末尾。
- O_CREAT：表示如果指定文件不存在，则创建这个文件
- O_EXCL ：表示如果要创建的文件已存在，则出错，同时返回 -1，并且修改 errno 的值。
- O_TRUNC：表示截断，如果文件存在，并且以只写、读写方式打开，则将其长度截断为0。
- O_NOCTTY：如果路径名指向终端设备，不要把这个设备用作控制终端。
- O_NONBLOCK：如果路径名指向 FIFO/块文件/字符文件，则把文件的打开和后继 I/O设置为非阻塞模式（nonblocking mode）

以下三个常量同样是选用的，它们用于同步输入输出

- O_DSYNC：等待物理 I/O 结束后再 write。在不影响读取新写入的数据的前提下，不等待文件属性更新。
- O_RSYNC：read 等待所有写入同一区域的写操作完成后再进行
- O_SYNC：等待物理 I/O 结束后再 write，包括更新文件属性的 I/O

### close函数

可用c l o s e函数关闭一个打开文件：

```
#include <unistd.h>
int close(int fd);
//返回值：若成功，返回0；若出错，返回-1；
```

### lseek函数

每个打开文件都有一个与其相关联的“当前文件位移量”。它是一个非负整数，用以度量
从文件开始处计算的字节数。(本节稍后将对“非负”这一修饰词的某些例外进行说明。)通常，
读、写操作都从当前文件位移量处开始，并使位移量增加所读或写的字节数。按系统默认，当
打开一个文件时，除非指定O _ A P P E N D选择项，否则该位移量被设置为0

```
#include <sys/types.h>
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence);
//返回值：若成功，返回新的文件偏移量；若出错，返回-1；
```

对参数offset 的解释与参数w h e n c e的值有关。
• 若w h e n c e是S E E K _ S E T，则将该文件的位移量设置为距文件开始处offset 个字节。
• 若w h e n c e是S E E K _ C U R，则将该文件的位移量设置为其当前值加offset, offset可为正或负。
• 若w h e n c e是S E E K _ E N D，则将该文件的位移量设置为文件长度加offset, offset可为正或负。

我们可以通过以下方式确定打开文件的偏移量：

```
off_t currpos;
currpos = lseek(fd, 0, SEEL_CUR);
```

这种方法也可以用来确定设计的文件是否可以设置偏移量，如果文件描述符指向的是一个管道、FIFO或网络套接字，则`lseek`返回-1，并将`errno`设置为`ESPIPE`。

### read

```
ssize_t read(int fd, void *buf, size_t nbytes);
//返回值：读到的字节数，若已到文件尾，返回0；若出错，返回-1；
```

有多种情况可使实际读到的字节数少于要求的字节数：

- 读普通文件时，在读到要求字节数之前已到达了文件末尾。
- 当从终端设备读时，通常一次最多读一行。
- 当从网络读时，网络中的缓冲机制可能造成返回值小于所要求的字节数。
- 当从管道或FIFO读时，如若管道包含的字节少于所需的数量，则只返回实际读到的字节数。
- 当从某些面向记录的设备读时，一次最多返回一个记录。
- 当一信号造成中断，而已经读了部分数据量时。

### write

```
ssize_t write(int fd, const void *buf, size_t nbytes);
//返回值：若成功，返回已写字节数；若出错，返回-1；
```

在一次写成功后，文件偏移数量增加实际写的字节数。

### dup\dup2函数

```
int dup(int fd);
int dup2(int fd, int fd2);
//两函数的返回值：若成功，返回新的文件描述符；若出错，返回-1；
```

由`dup`返回的新文件描述符一定数当前可用文件描述符中的最小值。对于`dup2`，可以用`fd2`参数指定新描述符的值。如果`fd2`已经打开，则先将其关闭。若`fd`等于`fd2`，则`dup2`返回`fd2`，而不关闭它。

这些函数返回的新文件描述符与参数`fd`共享同一个文件表项。且两个函数都为原子操作。

### fcntl

```
int fcntl(int fd, int cmd, [int arg]);
//返回值：若成功，则依赖于cmd；若出错，返回-1；
```

`fcntl`有以下5种功能：

- 复制一个已有的描述符（`cmd`=`F_DUPFD`或`cmd`=`F_DUPFD_CLOEXEC`）。

  不同点是，`F_DUPFD_CLOEXEC`会设置`CLOSE_ON_EXEC`，即当执行execve的时候，文件描述符将被关闭。

- 获取/设置文件描述符标志（`cmd`=`F_GETFD`或`cmd`=`F_SETFD`）。

- 获取/设置文件状态标志（`cmd`=`F_GETFL`或`cmd`=`F_SETFL`）。

- 获取/设置异步I/O所有权（`cmd`=`F_GETOWN`或`cmd`=`F_SETOWN`）。

- 获取/设置记录锁（`cmd`=`F_GETLK`、`F_SETLK`或`cmd`=`F_SETLKW`）。

### perror

```
void perror(const char *msg);
```

`perror`基于`errno`的当前值，在标准错误上产生一条出错消息，然后返回。它首先输出由`msg`指向的字符串，然后是一个冒号，一个空格，接着是对应于`errno`值得出错消息，最后是一个换行符