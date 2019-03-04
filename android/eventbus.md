1、如何使用：略
2、核心模式：观察者模式
3、图解
![](https://i.imgur.com/zNuRdf6.png)
4、版本更新：
EventBus2.x使用的是运行时注解，底层使用的是反射。
EventBus3.x使用的是编译时注解，底层使用的利用注解处理器动态生成代码，局部也使用反射，比如查找注册类的@Subscribe对应的方法。

6、概念梳理：（**其实都是对核心目标类的包装**）
> **METHOD_CACHE**：Map<Class<?>, List<SubscriberMethod>类型。键为注册类的 Class，值为该类中所有 EventBus 回调的方法链表（也就是被 @Subscribe 标记的方法们）。
> **Subscription**：关注类中两个字段，一个是 Object 类型的 subscriber，该字段即为注册的对象（在 Android 中时常为 Activity）；另一个是 SubscriberMethod 类型的 subscriberMethod
> **subscriberMethod**：SubscriberMethod 类型（文中称订阅方法）。关注类中有个字段 eventType 是 Class<?> 类型，代表 Event 的类类型。
> **subscribtionsByEventType**:Map<Class<?>, CopyonWriteArrayList<Subscribtion>> 类型。键为 Event 类型，值为元素为 Subscription（订阅信息）链表。核心字段.

PostingThreadState:ThreadLocal类EventBus#post(Object)时，控制同一个事件，避免重复被发送。

FindState：SubscriberMethodFinder的内部类，缓存复用（查找内存中各个关键对象的key），同时其内部属性记录订阅的相关信息（订阅者、订阅方法等）

SubscriberMethodFinder：订阅相关信息的查找和处理，相当于一个manager.

核心之外的技术点：订阅方法的缓存。
查找订阅方法时，ignoreGeneratedIndex 默认为false。

FIND_STATE_POOL：FindState缓冲池，在释放时赋值，在prepareFindState取出，

订阅方法，只能有一个参数，且该方法 must be public, non-static, and non-abstract。


**postSingleEvent():发送事件的核心方法，最后调用到postToSubscription()，根据订阅方法所在的线程来调用具体的调用方法，比如invokeSubscriber或者通知mainThreadPoster等线程调度对象，通过Handler机制，最终还是会调用invokeSubscriber()，最终利用反射，将事件返回给订阅者的订阅方法，实现事件的发送和传递**

eventInheritance：（发送事件的时候调用）默认为true，作用在相关代码注释有说，如果设为 true 的话，它会拿到 Event 父类的 class 类型，设为 false，可以在一定程度上提高性能。

粘性事件：sticky event
> sticky event，中文名为粘性事件。普通事件是先注册，然后发送事件才能收到；而粘性事件，在发送事件之后再订阅该事件也能收到。此外，粘性事件会保存在内存中，每次进入都会去内存中查找获取最新的粘性事件，除非你手动解除注册

优秀解释：
https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650242621&idx=1&sn=cc9c31aba5ff33b20fb9fecc3b0404b2&chksm=88638f52bf14064453fc09e6e53798ac5a1299d84df490b15764f9a85292732879eb4a1c0665&scene=38#wechat_redirect