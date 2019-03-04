转载来源：https://blog.csdn.net/freekiteyu/article/details/68932143
1、总览：
![](https://i.imgur.com/dEBGGWu.jpg)

2、基础概念
**Looper**：每个线程中最多只能有一个 Looper 对象，由 Looper 来管理此线程里的 MessageQueue (消息队列)。可以通过 Looper.myLooper() > 获取当前线程的 Looper 实例，通过 Looper.getMainLooper() 获取主（UI）线程的 Looper 实例。Lopper 会以无限循环的形式去查找是否有新消息，如果有就处理消息，否则就一直等待着。
> 
> **Handler**：你可以构造 Handler 对象来与 Looper 沟通，通过 push 发送新消息到 MessageQueue 里；或者通过 handleMessage 接收Looper 从 MessageQueue 取出来消息。
> 
> **MessageQueue**：MessageQueue是一个消息队列，内部存储了一组消息，以队列的形式对外提供插入和 删除的工作，内部采用单链表的数据结构来存储消息列表。***没有用系统提供的栈结构，而是自己封装的一个普通的对象，只是对应定义了当前Message的上下节点，而看起来像是链表结构。***
> 
> **ActivityThread**（其实并不在这个系统内，只是其中的一个样例）：我们经常提到的主线程，也叫UI线程，它就是 ActivityThread，主线程启动会默认初始化一个 Looper 并创建 Handler。一个线程中只有一个 Looper 实例，一个 MessageQueue 实例，可以有多个 Handler 实例。
> 
> **ThreadLocal**：一个线程内部的数据存储类，通过它可以在指定线程中存储数据，数据存储后，只有在指定线程中可以获取到存储的数据，对于其他线程来说无法获得数据。
> 对于 Handler 来说，它需要获取当前线程的 Looper ,而 Looper 的作用于就是线程并且不同的线程具有不同的 Looper ，通过 ThreadLocal 可以轻松实现线程中的存取。
> ThreadLocal原理：不同线程访问同一个ThreadLoacl的get方法，ThreadLocal的get方法会从各自的线程中取出一个数组，然后再从数组中根据当前ThreadLocal的索引去查找对应的Value值。

Message: 消息的实体类。

2、Looper工作机制
![](https://i.imgur.com/Kb1fSnd.png)

3、MessageQueue
> MessageQueue(boolean quitAllowed) {
> mQuitAllowed = quitAllowed;
> //构造函数，quitAllowed 用来标识是否允许退出。
> //主线程是不允许退出的（不然会退出整个程序），子线程可以退出。
> mPtr = nativeInit();
> }


