面试回答 ：https://juejin.im/entry/58cb4e3344d9040069f599aa

#布局
1.布局类型
	1. 五大布局（使用频率依次顺序）
	 RelativeLayout 、 LinearLayout 、 FrameLayout、AbsoluteLayout 、  TableLayout 
	2.约束性布局ConstraintLayout，AS2.3开始使用的页面默认布局，使用时需要导入库：
	com.android.support.constraint:constraint-layout:1.0.1，起功能类似于RelativeLayout，但嵌套更少，复杂页面上，其优势更加明显。
2.生命周期

	1、Activity生命周期
![](https://i.imgur.com/v1NR8XA.png)

	2、Fragment生命周期（见图）
![](https://i.imgur.com/mEKaNoB.jpg)

	3、数据缓存（系统的行为），onSaveInstanceState中保存数据，在onCreate或者onRestoreInstanceState（并不总是被执行，只有确定目标Activity被销毁了，才会在onStart后执行）方法中，通过Bundle键值对来恢复销毁前的数据。onSaveInstanceState被调用的场景：1、当用户按下HOME键时。2、长按HOME键，选择运行其他的程序时。3、按下电源按键（关闭屏幕显示）时。4、从activity A中启动一个新的activity时。5、横竖屏切换


3.启动流程
	>A、standand:A->B->C->D,启动D，则栈为A->B->C->D->D，即会重新创建
	>B、singleTop：A->B->C->D,启动D，则栈为A->B->C->D，即不会重新创建，但是启动B，因为B不在栈顶，则栈为A->B->C->D->B
	>C、singleTask，只有一个一个对应的Activity实例实例，A->B->C->D，启动B，则栈为A->B,“削顶”处理。
	>D、singleInstance，A->B->C启动D，D为singleInstance，则为D创建一个单独的task,原栈不变，D栈显示D页面，D再跳转到其他页面，则根据带跳转的页面的启动模式来显示。

4、Service

> a、启动方法
> public boolean bindService(Intent service,ServiceConnection conn,int flags)Service与启动他的Context的关联并启动，并且Service的生命周期依附于Context
> 	public ComponentName startService(Intent service)此时Service的生命周期与启动它的Context无关。
> b、保证service不被杀死：
> 1、Service设置成START_STICKY，其在kill 后会被重启（等待5秒左右），重传Intent，保持与重启前一样
> 2、提升service优先级，intent-filter可以通过android:priority = "1000"，
> 3、提升service进程优先级，设置启动的进程为前台进程。
> 4、在service的onDestroy方法里重启service。
> 5、箭头系统广播，比如手机重启、界面唤醒、应用状态改变等等 ，启动service
> 6、在JNI层,用C代码fork一个进程出来，系统会认为这个是系统进程不杀死。
> 7、root之后放到system/app变成系统级应用
> 8、大招: 放一个像素在前台(手机QQ)

5.Context区别
> 1、Activity和Service以及Application的Context是不一样的,Activity继承自ContextThemeWraper.其他的继承自ContextWrapper
> 2、 每一个Activity和Service以及Application的Context都是一个新的ContextImpl对象
> 3、getApplication()用来获取Application实例的，但是这个方法只有在Activity和Service中才能调用的到。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法，getApplicationContext()比getApplication()方法的作用域会更广一些，任何一个Context的实例，只要调用getApplicationContext()方法都可以拿到我们的Application对象。
> 4、Activity在创建的时候会new一个ContextImpl对象并在attach方法中关联它，Application和Service也差不多。ContextWrapper的方法内部都是转调ContextImpl的方法
> 5、创建对话框传入Application的Context是不可以的
> 6、尽管Application、Activity、Service都有自己的ContextImpl，并且每个ContextImpl都有自己的mResources成员，但是由于它们的mResources成员都来自于唯一的ResourcesManager实例，所以它们看似不同的mResources其实都指向的是同一块内存
> 7、Context的数量等于Activity的个数 + Service的个数 + 1，这个1为Application

6、APP启动流程
> 比喻：AMS是董事会，负责指挥和调度的，ActivityThread是老板，虽然说家里的事自己说了算，但是需要听从AMS的指挥，而Instrumentation则是老板娘，负责家里的大事小事，但是一般不抛头露面，听一家之主ActivityThread的安排

7、进程类型
> 1、前台进程：即与用户正在交互的Activity或者Activity用到的Service等，如果系统内存不足时前台进程是最后被杀死的
> 2、可见进程：可以是处于暂停状态(onPause)的Activity或者绑定在其上的Service，即被用户可见，但由于失去了焦点而不能与用户交互
> 3、服务进程：其中运行着使用startService方法启动的Service，虽然不被用户可见，但是却是用户关心的，例如用户正在非音乐界面听的音乐或者正在非下载页面自己下载的文件等；当系统要空间运行前两者进程时才会被终止
> 4、后台进程：其中运行着执行onStop方法而停止的程序，但是却不是用户当前关心的，例如后台挂着的QQ，这样的进程系统一旦没了有内存就首先被杀死
> 5、空进程：不包含任何应用程序的程序组件的进程，这样的进程系统是一般不会让他存在的
## 如何避免后台进程被杀死 ##：

> 调用startForegound，让你的Service所在的线程成为前台进程
> Service的onStartCommond返回START_STICKY或START_REDELIVER_INTENT
> Service的onDestroy里面重新启动自己
8、开机过程
> 1. BootLoder引导,然后加载Linux内核.
> 2. 0号进程init启动.加载init.rc配置文件,配置文件有个命令启动了zygote进程
> 3. zygote开始fork出SystemServer进程
> 4. SystemServer加载各种JNI库,然后init1,init2方法,init2方法中开启了新线程ServerThread.
> 5. 在SystemServer中会创建一个socket客户端，后续AMS（ActivityManagerService）会通过此客户端和zygote通信
> 6. ServerThread的run方法中开启了AMS,还孵化新进程ServiceManager,加载注册了一溜的服务,最后一句话进入loop 死循环
> 7. run方法的SystemReady调用resumeTopActivityLocked打开锁屏界面

9、Serializable与Parcelable区别
> Serializable使用IO读写存储在硬盘上。序列化过程使用了反射技术，并且期间产生临时对象。优点代码少。
> 
> Parcelable是直接在内存中读写，我们知道内存的读写速度肯定优于硬盘读写速度，所以Parcelable序列化方式性能上要优于Serializable方式很多。但是代码写起来相比Serializable方式麻烦一些。

10、加载的过程 
> 类加载到虚拟机，分为加载，验证，准备，解析和初始化这五个阶段

(下面图片来源：https://blog.csdn.net/freekiteyu/article/details/79318031)
11、开机启动过程
![](https://i.imgur.com/rmeFRtO.png)

12、点击手机上应用图标产生的流程 
![](https://i.imgur.com/YnMxYNr.png)

13、Activity 的整体启动流程
![](https://i.imgur.com/sbiyGHd.jpg)

## 14、并发与并行的理解 ##
并发和并行都可以处理“多任务”，二者的主要区别在于是否是“同时进行”多个的任务。
 
> 假设一个有三个学生需要辅导作业，帮每个学生辅导完作业是一个任务
   **顺序执行**：老师甲先帮学生A辅导，辅导完之后再取给B辅导，最后再去给C辅导，效率低下 ，很久才完成三个任务
   **并发**：老师甲先给学生A去讲思路，A听懂了自己书写过程并且检查，而甲老师在这期间直接去给B讲思路，讲完思路再去给C讲思路，让B自己整理步骤。这样老师就没有空着，一直在做事情，很快就完成了三个任务。与顺序执行不同的是，顺序执行，老师讲完思路之后学生在写步骤，这在这期间，老师是完全空着的，没做事的，所以效率低下。
   **并行**：直接让三个老师甲、乙、丙三个老师“同时”给三个学生辅导作业，也完成的很快
