参考链接自1：https://juejin.im/entry/57fde493d203090068e4556d
参考：https://juejin.im/post/5a7f9bb86fb9a0634b4d724b

1、这是一个网络通信的框架，适合于数据不大但通信频繁的应用场景。

2、与其他框架优缺点对比：https://blog.csdn.net/zhangcanyan/article/details/51793951

3、缺点：
> 使用的是httpclient、HttpURLConnection
> 6.0不支持httpclient了，如果想支持得添加org.apache.http.legacy.jar
> 对大文件下载 Volley的表现非常糟糕
> 只支持http请求
> 图片加载性能一般

如何使用：
` RequestQueue requestQueue = Volley.newRequestQueue(context);

  StringRequest stringRequest = new StringRequest(url
           , new Response.Listener<String>() {
       @Override
       public void onResponse(String s) {
           // TODO：
       }
   }, new Response.ErrorListener() {
       @Override
       public void onErrorResponse(VolleyError error) {
          // TODO：
       }
   });
   requestQueue.add(stringRequest);`



4、Http协议基础：
请求：
![](https://i.imgur.com/JJr3qiq.png)
响应：
![](https://i.imgur.com/6bGqpMv.png)

5、主流程
> 1、Volley 运行的过程中一共有三种线程，包括 UI 线程、Cache 调度线程和 NetWork 调度线程池
> 2、请求加入优先级队列，Cache 线程进行筛选，如果命中（hit）分发给 UI 线程
> 未命中（miss）交给 NetWork 调度线程池处理，取回后更新 Cache 并分发给 UI 线程
> 3、每次请求执行过程始于 UI 线程， 终于 UI 线程

6、主要类：
> Volley             对外暴露的 API，主要作用是构建 RequestQueue
> 
> Request            所有网络请求的抽象类，StringRequest、JsonRequest、ImageRequest 都是它的子类
> 
> RequestQueue       存放请求的队列，里面包括 CacheDispatcher、NetworkDispatcher 和 ResponseDelivery
> 
> Response           封装一个解析后的结果以便分发
> 
> CacheDispatcher    用于执行缓存队列请求的线程
> 
> NetworkDispatcher  用户执行网络队列请求的线程
> 
> Cache              缓存请求结果，Volley 默认使用的是基于 sdcard 的 DiskBaseCache
> 
> HttpStack          处理 Http 请求（## **最终的请求处理者** ##），并返回请求结果
> 
> Network            调用 HttpStack 处理请求，并将结果转换成可被 ResponseDelivery 处理的 NetworkResponse
>  
> ResponseDelivery   返回结果的分发接口

## 源码相对而言，可以看懂 ##

看图：
![](https://i.imgur.com/OAXNjNI.png)

缓存机制
分发者：CacheDispatcher
请求的队列容器：mNetworkQueue

Volley.newRequestQueue()--->在RequestQueue的start()方法中，开启CacheDispatcher的轮询，查找request--->RequestQueue的实例对象调用add()添加请求，加入到缓存mCacheQueue--->有缓存的请求则执行请求，并返回结果及调用方，并缓存最终的结果。

![](https://i.imgur.com/fZbxfPX.png)