Dalvik
========================================

Dalvik虚拟机是Android程序的虚拟机，是Android中Java程序的运行基础。其指令集基于寄存器架构，
执行其特有的文件格式——dex字节码来完成对象生命周期管理、堆栈管理、线程管理、安全异常管理、
垃圾回收等重要功能。它的核心内容是实现库（libdvm.so），大体由C语言实现。依赖于Linux内核
的一部分功能——线程机制、内存管理机制，能高效使用内存，并在低速CPU上表现出的高性能。每一个
Android应用在底层都会对应一个独立的Dalvik虚拟机实例，其代码在虚拟机的解释下得以执行。

一个应用，一个虚拟机实例，一个进程！！！

每一个Android应用都运行在一个Dalvik虚拟机实例里，而每一个虚拟机实例都是一个独立的进程空间。
每个进程之间可以通信（IPC，Binder机制实现）。虚拟机的线程机制，内存分配和管理，Mutex等等都是
依赖底层操作系统而实现的。

不同的应用在不同的进程空间里运行，当一个虚拟机关闭或意外中止时不会对其它虚拟机造成影响，可以
最大程度的保护应用的安全和独立运行。

Zygote是虚拟机实例的孵化器。AndroidRuntime.cpp中ZygoteInit.main()的执行会完成一个分裂，分裂出来的
子进程继续初始化Java层的架构，这个分裂出来的进程就是system_server。每当系统要求执行一个Android
应用程序，Zygote就会FORK出一个子进程来执行该应用程序。这样做的好处显而易见：
Zygote进程是在系统启动时产生的，它会完成虚拟机的初始化，库的加载，预置类库的加载和初始化等等操作，
而在系统需要一个新的虚拟机实例时，Zygote通过复制自身，最快速的提供个系统。另外，对于一些只读的
系统库，所有虚拟机实例都和Zygote共享一块内存区域，大大节省了内存开销。

AndroidRuntime.start
----------------------------------------

https://github.com/leeminghao/about-android/blob/master/dalvik/start/start.md