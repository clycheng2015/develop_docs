spingBoot学习:
http://blog.didispace.com/Spring-Boot%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/

1、项目打包：
项目根目录下打开cmd，执行:mvn package，在target目录下生成jar包.

2、带命令执行构建后的jar包
java -jar spring-boot-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=test --my1.age=32，--开头的都是全局配置参数。

3、自定义属性配置：https://blog.csdn.net/winter_chen001/article/details/80537874

4、redis使用
http://www.cnblogs.com/jiangbei/p/8601107.html

5、缓存spring-boot-starter-cache
http://www.cnblogs.com/yueshutong/p/9381540.html