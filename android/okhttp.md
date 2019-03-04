引用1：https://juejin.im/post/5c47e14a6fb9a049ac797f95

API（网络层）层设计思想：选择OkHttp发送请求，使用Kotlin Coroutine处理响应，用LiveData来通知UI更新；将这些逻辑抽象为VM层，具体表现为ViewModel，代表性开源库：https://github.com/li-xiaojun/AndroidKTX

核心：责任链模式、socket通讯
