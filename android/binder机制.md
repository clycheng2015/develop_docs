## 源博文地址：https://blog.csdn.net/freekiteyu/article/details/70082302 ##
## 1、什么是 Binder？（C/S架构的通讯） ##

> Binder是Android系统中## *# 进程间通讯 #* ##（IPC）的一种方式，也是Android系统中最重要的特性之一。Android中的四大组件Activity，Service，Broadcast，ContentProvider，不同的App等都运行在不同的进程中，它是这些进程间通讯的桥梁。正如其名“粘合剂”一样，它把系统中各个组件粘合到了一起，是各个组件的桥梁。

## 2、Binder架构图解 ##
![](https://i.imgur.com/b75kiUT.jpg)

## 3、Binder 机制 ##
![](https://i.imgur.com/wI1JQ8V.jpg)
具体流程：
> a、首先需要注册服务端，只有注册了服务端，客户端才有通讯的目标，服务端通过 ServiceManager 注册服务，注册的过程就是向 Binder 驱动的全局链表 binder_procs 中插入服务端的信息（binder_proc 结构体，每个 binder_proc 结构体中都有 todo 任务队列），然后向 ServiceManager 的 svcinfo 列表中缓存一下注册的服务。
> 
> b、有了服务端，客户端就可以跟服务端通讯了，通讯之前需要先获取到服务，拿到服务的代理，也可以理解为引用。获取服务端的方式就是通过 ServiceManager 向 svcinfo 列表中查询一下返回服务端的代理，svcinfo 列表就是所有已注册服务的通讯录，保存了所有注册的服务信息
> 
> c、有了服务端的引用我们就可以向服务端发送请求了，通过 BinderProxy 将我们的请求参数发送给 ServiceManager，通过**共享内存**的方式使用内核方法 copy_from_user() 将我们的参数先拷贝到**内核空间**，这时我们的客户端进入等待状态，然后 Binder 驱动向服务端的 todo 队列里面插入一条事务，执行完之后把执行结果通过 copy_to_user() 将内核的结果拷贝到**用户空间**（这里只是执行了拷贝命令，并没有拷贝数据，binder只进行一次拷贝），唤醒等待的客户端并把结果响应回来，这样就完成了一次通讯。

## 重要概念 ##
> 用户空间：只能执行简单的运算，不能直接调用系统资源，必须通过访问内核空间的唯一方式进行系统调用，才能向内核发出指令。
> 内核空间：是 Linux 内核的运行空间，可以执行任意命令，调用系统的一切资源，两者逻辑上是隔离的。
> 内核态：当一个任务（进程）执行系统调用而陷入内核代码中执行时的状态，此时处理器处于特权级最高的（0级）内核代码中执行。
> 用户态：进程在执行用户自己的代码时，此时处理器在特权级最低的（3级）用户代码中运行。处理器在特权等级高的时候才能执行那些特权CPU指令。
> Binder 驱动：在 Android 系统中，这个运行在内核空间的，负责各个用户进程（用户空间）通过 Binder 通信的**内核模块（参考下面的描述）**，Binder内核这里可以理解为“硬件”，Binder 驱动模块可以理解为用户程序与此硬件进行通讯的特殊程序。

## 用户空间想与另外一个用户空间进行通信？##
> 传统的 Linux 通信机制，比如 Socket，管道等都是内核支持的；但是 Binder 并不是 Linux 内核的一部分，它是怎么做到访问内核空间的呢？ Linux 的动态可加载内核模块（Loadable Kernel Module，LKM）机制解决了这个问题；该模块是具有独立功能的程序，它可以被单独编译，但不能独立运行。它在运行时被链接到***内核作为内核的一部分在内核空间***运行。这样，Android系统可以通过添加一个内核模块运行在内核空间，用户进程之间的通过这个模块作为桥梁，就可以完成通信了。


## 4、Binder 驱动 ##
![](https://i.imgur.com/TPRpApR.jpg)

##  5、Binder 进程与线程 ##
![](https://i.imgur.com/QSaih3a.jpg)
进程与线程图解：
> 对于底层Binder驱动，通过 binder_procs 链表记录所有创建的 binder_proc 结构体，binder 驱动层的每一个 binder_proc 结构体都与用户空间的一个用于 binder 通信的进程一一对应，且每个进程有且只有一个 ProcessState 对象，这是通过单例模式来保证的。在每个进程中可以有很多个线程，每个线程对应一个 IPCThreadState 对象，IPCThreadState 对象也是单例模式，即一个线程对应一个 IPCThreadState 对象，在 Binder 驱动层也有与之相对应的结构，那就是 Binder_thread 结构体。在 binder_proc 结构体中通过成员变量 rb_root threads，来记录当前进程内所有的 binder_thread。

**BINDER 线程池**
每个 Server 进程在启动时创建一个 binder 线程池，并向其中注册一个 Binder 线程；之后 Server 进程也可以向 binder 线程池注册新的线程，或者 Binder 驱动在探测到没有空闲 binder 线程时主动向 Server 进程注册新的的 binder 线程。对于一个 Server 进程有一个最大 Binder 线程数限制，默认为16个 binder 线程，例如 Android 的 system_server 进程就存在16个线程。对于所有 Client 端进程的 binder 请求都是交由 Server 端进程的 binder 线程来处理的。


## 6、ServiceManager 启动 ##
ServiceManager 的作用很简单就是提供了查询服务和注册服务的功能。
![](https://i.imgur.com/15kml2f.jpg)
> 1、ServiceManager 分为 framework 层和 native 层，framework 层只是对 native 层进行了封装方便调用，图上展示的是 native 层的 ServiceManager 启动过程。
> 
> 2、ServiceManager 的启动是系统在开机时，init 进程解析 init.rc 文件调用 service_manager.c 中的 main() 方法入口启动的。 native 层有一个 binder.c 封装了一些与 Binder 驱动交互的方法。
> 
> 3、ServiceManager 的启动分为三步，首先打开驱动创建全局链表 binder_procs，然后将自己当前进程信息保存到 binder_procs 链表，最后开启 loop 不断的处理共享内存中的数据，并处理 BR_xxx 命令（ioctl 的命令，BR 可以理解为 binder reply 驱动处理完的响应）。

## 7、ServiceManager 注册服务 ##
![](https://i.imgur.com/wZUvE2i.jpg)

> 1、注册 MediaPlayerService 服务端，我们通过 ServiceManager 的 addService() 方法来注册服务。


> 2、首先 ServiceManager 向 Binder 驱动发送 BC_TRANSACTION 命令（ioctl 的命令，BC 可以理解为 binder client 客户端发过来的请求命令）携带 ADD_SERVICE_TRANSACTION 命令，同时注册服务的线程进入等待状态 waitForResponse()。 Binder 驱动收到请求命令向 ServiceManager 的 todo 队列里面添加一条注册服务的事务。事务的任务就是创建服务端进程 binder_node 信息并插入到 binder_procs 链表中。

> 3、事务处理完之后发送 BR_TRANSACTION 命令，ServiceManager 收到命令后向 svcinfo 列表中添加已经注册的服务。最后发送 BR_REPLY 命令唤醒等待的线程，通知注册成功。

## 8、获取服务 ##
（上图红色箭头部分）
1、获取服务的过程与注册类似，相反的过程。通过 ServiceManager 的 getService() 方法来注册服务。

2、首先 ServiceManager 向 Binder 驱动发送 BC_TRANSACTION 命令携带 CHECK_SERVICE_TRANSACTION 命令，同时获取服务的线程进入等待状态 waitForResponse()。

3、Binder 驱动收到请求命令向 ServiceManager 的发送 BC_TRANSACTION 查询已注册的服务，查询到直接响应 BR_REPLY 唤醒等待的线程。若查询不到将与 binder_procs 链表中的服务进行一次通讯再响应。

