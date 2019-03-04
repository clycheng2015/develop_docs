#面试相关
1、资源参考
优秀博文：http://www.androidblog.cn/
开源项目：http://www.androidblog.cn/
2、Java基础
> 1、动态代理，其实就是两次静态代理，详细来说，就是“实际是双层的静态代理，开发者提供了委托类 B，程序动态生成了代理类 A。开发者还需要提供一个实现了InvocationHandler的子类 C，子类 C 连接代理类 A 和委托类 B，它是代理类 A 的委托类，委托类 B 的代理类。用户直接调用代理类 A 的对象，A 将调用转发给委托类 C，委托类 C 再将调用转发给它的委托类 B”

2、启动模式
>A、standand:A->B->C->D,启动D，则栈为A->B->C->D->D，即会重新创建
>B、singleTop：A->B->C->D,启动D，则栈为A->B->C->D，即不会重新创建，但是启动B，因为B不在栈顶，则栈为A->B->C->D->B
>C、singleTask，只有一个一个对应的Activity实例实例，A->B->C->D，启动B，则栈为A->B,“削顶”处理。
>D、singleInstance，A->B->C启动D，D为singleInstance，则为D创建一个单独的task,原栈不变，D栈显示D页面，D再跳转到其他页面，则根据带跳转的页面的启动模式来显示。



3、项目分析



4、开源框架分析


