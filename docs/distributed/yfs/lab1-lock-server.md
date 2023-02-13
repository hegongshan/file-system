# Lab 1: Lock Server

### 准备工作

首先，我们需要下载Lab1的源代码：

```bash
$ git clone https://pdos.csail.mit.edu/archive/6.824-2012/yfs-class.git
```

接着，尝试编译源代码：

```bash
$ make
```

此时，编译器会产生如下错误信息：

```bash
rpc/rpc.cc:98:32: error: ‘getpid’ was not declared in this scope; did you mean ‘getpt’?
```

不要慌，产生这种错误的原因是代码中没有引用对应的头文件。getpid()的函数申明在unistd.h中，因此，我们只需要在`rpc/rpc.h`中引用该头文件，就可以修复上述错误。

![](../../img/yfs-lab1-lock-server-rpc.png)



（未完待续）

### 参考文献

1. https://pdos.csail.mit.edu/archive/6.824-2012/labs/lab-1.html