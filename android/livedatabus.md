项目分析和对比：https://juejin.im/post/5c48038f518825253b5eb81d
美团：https://tech.meituan.com/2018/07/26/android-livedatabus.html，
开源并修复版：https://github.com/JeremyLiao/LiveDataBus 。
Android Architecture Components系列：https://www.jianshu.com/p/84a3442955ae

> **消息** 消息可以是任何的Object，可以定义不同类型的消息，如Boolean、String。也可以定义自定义类型的消息。
> **消息通道** LiveData扮演了消息通道的角色，不同的消息通道用不同的名字区分，名字是String类型的，可以通过名字获取到一个LiveData消息通道。
> **消息总线** 消息总线通过单例实现，不同的消息通道存放在一个HashMap中。
> **订阅** 订阅者通过getChannel获取消息通道，然后调用observe订阅这个通道的消息。
> **发布** 发布者通过getChannel获取消息通道，然后调用setValue或者postValue发布消息。

## LiveDataBus源码 ##

    public final class LiveDataBus {

    private final Map<String, MutableLiveData<Object>> bus;

    private LiveDataBus() {
        bus = new HashMap<>();
    }

    private static class SingletonHolder {
        private static final LiveDataBus DATA_BUS = new LiveDataBus();
    }

    public static LiveDataBus get() {
        return SingletonHolder.DATA_BUS;
    }

    public <T> MutableLiveData<T> getChannel(String target, Class<T> type) {
        if (!bus.containsKey(target)) {
            bus.put(target, new MutableLiveData<>());
        }
        return (MutableLiveData<T>) bus.get(target);
    }

    public MutableLiveData<Object> getChannel(String target) {
        return getChannel(target, Object.class);
    }
}

事件订阅：
    
    LiveDataBus.get().getChannel("key_test", Boolean.class)
        .observe(this, new Observer<Boolean>() {
            @Override
            public void onChanged(@Nullable Boolean aBoolean) {
            }
        });

事件发送：

    LiveDataBus.get().getChannel("key_test").setValue(true);


