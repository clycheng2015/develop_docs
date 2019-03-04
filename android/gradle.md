引用：https://juejin.im/entry/57c7a00e0a2b58006b1a1358

编译过程：
> **初始化阶段**：创建 Project 对象，如果有多个build.gradle，也会创建多个project.
> **配置阶段**：在这个阶段，会执行所有的编译脚本，同时还会创建project的所有的task，为后一个阶段做准备。
> **执行阶段**：在这个阶段，gradle 会根据传入的参数决定如何执行这些task,真正action的执行代码就在这里.

## 项目基础gradle ##

![](https://i.imgur.com/sF7cv3X.png)

> **setting.gradle**:这个 setting 文件定义了哪些module 应该被加入到编译过程，对于单个module 的项目可以不用需要这个文件，但是对于 multimodule 的项目我们就需要这个文件，否则gradle 不知道要加载哪些项目。这个文件的代码在初始化阶段就会被执行。
> **顶层的build.gradl**e:顶层的build.gradle文件的配置最终会被应用到所有项目中。其中**buildscript**：定义了 Android 编译工具的类路径。repositories中,jCenter是一个著名的 Maven 仓库。**allprojects**:中定义的属性会被应用到所有 moudle 中，但是为了保证每个项目的独立性，我们一般不会在这里面操作太多共有的东西。
> **项目单独的 build.gradle**：针对每个moudle 的配置，如果这里的定义的选项和顶层build.gradle定义的相同，后者会被覆盖。典型的 配置内容如下

![](https://i.imgur.com/1wpgbx6.png)
![](https://i.imgur.com/LdLoTIQ.png)


> **apply plugin**:第一行代码应用了Android 程序的gradle插件，作为Android 的应用程序，这一步是必须的，因为plugin中提供了Android 编译、测试、打包等等的所有task。
> **android**:这是编译文件中最大的代码块，关于android 的所有特殊配置都在这里，这就是又我们前面的声明的 plugin 提供的。
> defaultConfig就是程序的默认配置，注意，如果在AndroidMainfest.xml里面定义了与这里相同的属性，会以这里的为主。
> 这里最有必要要说明的是applicationId的选项：在我们曾经定义的AndroidManifest.xml中，那里定义的包名有两个用途：一个是作为程序的唯一识别ID,防止在同一手机装两个一样的程序；另一个就是作为我们R资源类的包名。在以前我们修改这个ID会导致所有用引用R资源类的地方都要修改。但是现在我们如果修改applicationId只会修改当前程序的ID,而不会去修改源码中资源文件的引用。
> **buildTypes**:定义了编译类型，针对每个类型我们可以有不同的编译配置，不同的编译配置对应的有不同的编译命令。默认的有debug、release 的类型。
> **dependencies**:是属于gradle 的依赖配置。它定义了当前项目需要依赖的其他库

**Gradle Wrapper**：版本兼容实现
![](https://i.imgur.com/pDL9R4P.png)


**Android tasks**
> **assemble**：对所有的 buildType 生成 apk 包,默认会依赖子任务assembleDebug 和assembleRelease，直接执行assmeble，最后会编译debug，和release 的所有版本出来。
> **clean**:移除所有的编译输出文件，比如apk
> **check**:执行lint检测编译。
> **build**:同时执行assemble和check命令
> **install**：会将编译后的apk 安装到连接的设备

这些都是基本的命令，在实际项目中会根据不同的配置，会对这些task 设置不同的依赖。比如 默认的 assmeble 会依赖 assembleDebug 和assembleRelease，如果直接执行assmeble，最后会编译debug，和release 的所有版本出来。如果我们只需要编译debug 版本，我们可以运行assembleDebug。

备注：
**Groovy** 是一种 JVM 语言，JVM 语言的特征就是，虽然编写的语法不一样，但是他们最终都会编程 JVM 字节码文件。同是JVM 语言的还有 Scala,Kotlin 等等

BuildConfig：通过buildConfigField字段，根据不同APK环境配置全局的变量，最终会在BuildConfig文件中，生成类似于BuildConfig.DEBUG的字段。

![](https://i.imgur.com/KxgKGSw.png)
也可以生成资源字段，其生成的字段可以类似于在 res/values/xxx.xml文件中的代码类似，类型必须是字符串。
![](https://i.imgur.com/kamoUdf.png)


## **Repositories** 依赖代码所在的代码仓库 ##
远程仓库（可能需要用户名及密码）
![](https://i.imgur.com/F94Gyse.png)
本地依赖
![](https://i.imgur.com/7VPAfxc.png)

## **Dependencies** ##
File dependencies---通过files()或者fileTree()方法可以添加文件依赖。
![](https://i.imgur.com/NsDOuYj.png)
Native libraries---配置本地 .so库文件依赖
![](https://i.imgur.com/yVQfoKb.png)
Library projects----要写一个library项目让其他的项目引用，我们的bubild.gradle的plugin相应的改为apply plugin: 'com.android.library'，然后在setting文件中include即可。，如果是以外部文件形式引用，则可以如下操作：
aar包依赖：
**![](https://i.imgur.com/j3LfXUK.png)**
当需要引用里面的某个项目时，通过如下方式引用：
![](https://i.imgur.com/jppUcwQ.png)

## Build Variants 动态编译APK##
1、Build Type---比如，想在默认的类型基础上，新增一个的statging的编译类型：
![](https://i.imgur.com/sengPF9.png)
每当创建一个新的build type 的时候，gradle 默认都会创建一个新的source set，我们可以建立与main文件夹同级的文件夹，根据编译类型的不同我们可以选择对某些源码或资源文件直接进行替换。
![](https://i.imgur.com/yH0dnGP.png)
同时，不同编译类型的项目，我们的依赖都可以不同：
![](https://i.imgur.com/dY2QGJg.png)

## Product flavors同一份源码编译不同的程序（包名也不同），比如 免费版和收费版 ##

**注意**：Product flavors和Build Type是不一样的，而且他们的属性也不一样。所有的 product flavor 版本和defaultConfig 共享所有属性！

1、像Build type 一样，product flavor 也可以有自己的source set文件夹。除此之外，product flavor 和 build type 可以结合，他们的文件夹里面的文件优先级甚至高于 单独的built type 和product flavor 文件夹的优先级。如果你想对于 blue类型的release 版本有不同的图标，我们可以建立一个文件夹叫blueRelease，注意，这个顺序不能错，一定是 flavor+buildType 的形式。

更复杂的情况下，我们可能需要多个product 的维度进行组合，比如我想要 color 和 price 两个维度去构建程序。这时候我们就需要使用flavorDimensions：
![](https://i.imgur.com/WImuzLA.png)
![](https://i.imgur.com/0Cc7N0S.png)
执行编译之后，根据我们的配置，再次查看我们的task,发现多了这些task:
![](https://i.imgur.com/Kk1Kyrz.png)

## Signing configurations签名配置 ##
1、我们打包市场版的时候，我们需要输入我们的keystore数据。如果是debug 版本，系统默认会帮我们配置这些信息。这些信息在gradle 中都配置在signingConfigs中
![](https://i.imgur.com/BMqGVVv.png)
2、配置之后我们需要在build type中直接使用
![](https://i.imgur.com/iMyryIb.png)

## Optimize编译优化 ##
1、开启并行编译：在项目根目录下面的 gradle.properties中设置
> org.gradle.parallel=true。

2、开启编译守护进程：该进程在第一次启动后回一直存在，当你进行二次编译的时候，可以重用该进程。同样是在gradle.properties中设置
> org.gradle.daemon=true
> org.gradle.jvmargs=-Xms256m -Xmx1024m

3、shrinkResources
![](https://i.imgur.com/nTgrcGz.png)

## 其他 ##
1、Profiling---我们执行所有task的时候我们都可以通过添加--profile参数生成一份执行报告在reports/profile中：
![](https://i.imgur.com/eOE3EPi.png)

**命令行操作**
> gradlew tasks           列出所有task
> gradlew tasks -all      列出每个task 对应依赖的其他task
> gradlew shrinkReleaseResources  查看shrinkResources命令帮我们减少了多少无用的资源
