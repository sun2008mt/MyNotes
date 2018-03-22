# 1. 介绍
## 1.1 应用基础（Application Fundamentals）
安卓操作系统是一个多用户的Linux系统，每个app分别是不同的用户

默认情况下，系统会为每个app分配一个唯一的Linux用户ID（只有系统才知道），系统会为根据不同的用户ID为app的安装目录下文件设置权限，只有对应该用户ID的app才能访问相应目录的文件

默认情况下，每个应用都运行在自己的Linux进程下，在内存不足时系统会关闭某些进程以腾出空间给新运行的应用

安卓系统遵循principle of least privilege原则，每个应用只会获取它完成任务所需要的权限而已，因而会很安全

两个app可以分享同一个Linux用户ID，这样能够让它们访问彼此的数据文件等，需要它们通过同样的认证（sign the same certificate）。为了保护系统资源，拥有相同用户ID的app能够运行在同一个Linux进程下，也能分享同样的VM

四大基本组件（Activities、Services、Broadcast receivers和Content providers）是系统或用户进入app的入口

通过Intent来启动Activities、Services和Broadcast receivers，可以使用隐式（implicit）Intent和显示（explicit）Intent。隐式Intent可以让系统找到设备上的某些能执行该任务的应用或组件供用户使用

Content providers是通过ContentResolver的请求启动的

启动Services时使用隐式Intent是一个危险的行为（security hazard），因为无法确定哪个服务会对服务做出回应，用户也无法看到哪些服务启动了

系统通过对比接收的Intent和组件中通过<intent-filter>声明的内容来判断哪些组件可以接收该Intent

由于不同的app是运行在相互独立的进程中的，因此一个app并不能启动其它app，不过系统可以，因此可以发送消息给系统，系统通过发送的Intent启动相应的组件

在app组件启动之前，系统需要通过读取AndroidManifest.xml文件了解哪些组件存在，因而app组件需要在AndroidManifest.xml文件中声明，它的其它作用包括：
<pre>
（1）声明app所需的权限

（2）声明app所需的最小和最大API级别

（3） 声明app所需的硬件和软件特征，如相机、蓝牙服务或者多点触控屏幕等

（4） Declares API libraries the app needs to be linked against (other than the Android framework APIs), such as the Google Maps libraries
</pre>
除了broadcast receivers既可以在Manifest文件中声明也可以在代码中动态创建之外，其它三种组件必须在Manifest文件中声明，否则是对系统不可见的因而永远也不能运行

如果某应用需要使用相机并且使用API Level 7以上，需要声明如下，主要不是App来读取，而是方便一些外部服务（如Google Play）做出分类等
```
<use-feature android:name="android.hardware.camera.any" android:required="true" />
<use-sdk android:minSdkVersion="7" android:targetSdkVersion="19" />
```
## 1.2 设备兼容性
如果程序需要硬件设备的支持，需要在Manifest文件中声明，如果不是硬性要求，可以在声明时将required属性设为false
```
<use-feature android:name="android.hardware.sensor.compass" android:required="false" />
```
在代码中使用hasSystemFeature方法检查是否有当前所需特性支持
```
PackageManager pm = getPackageManager();
if (!pm.hasSystemFeature(PackageManager.FEATURE_SENSOR_COMPASS)) {
    //设备没有指南针

}
```
一些系统权限隐式地表明需要设备的某个特性，如访问蓝牙权限隐式表明需要FEATURE_BLUETOOTH的设备特征

使用minSdkVersion和targetSdkVersion来声明最小API级别和目标API级别

<use-sdk android:minsdkVersion="14" android:targetSdkVersion="19" />

targetSdkVerison不会阻止你将应用安装在更高API级别的设备上，不过系统会只保持目标API级别的一些特性处理方式，如API 19对AlarmManager创建闹钟的机制做了一些改变，如果targetSdkVersion值比19低的话，系统会按原来的处理方式

## 1.3 系统权限
安卓软件栈
共分为六层：
<pre>
（1）Linux内核（Linux Kernel）： 安卓平台的基础，如ART（Android Runtime）依赖Linux内核处理线程和底层内存管理的任务。使用Linux内核使得Android拥有了Linux一样的安全特性，而且能让设备生产商提一个供统一内核的硬件驱动；

（2）硬件抽象层HAL（Hardware Abstraction Layer）：HAL给高层的Java API Framework暴露了设备所具有的硬件的标准接口。HAL由多个库组成，每一个库都实现了特定类型硬件组件的一个接口，例如相机和蓝牙模块。当某个FrameWork API发送了访问设备硬件的请求时，Android系统会加载对应该硬件组件的库模块；

（3）Android运行时（Android Runtime）：在Android 5.0（API级别21）以上，每个应用都运行着自己的进程，拥有自己的ART实例。ART通过执行DEX（一种为Android定制的字节码文件，为最小的内存占用优化）文件在小内存设备上运行多个虚拟机。构建工具链，如Jack，将Java源代码编译成DEX字节码后，可以在Android平台上运行。

ART的特性包括：提前（Ahead-of-time）AOT和及时（Just-in-time）JIT编译模式、优化垃圾回收（Gabage Collection）、更好的调试支持等。

在Android5.0之前，Dalvik是Android运行时，在ART上能运行的也可以在Dalvik虚拟机上运行，不过反过来就不一定了。

Android运行时也包含了一系列的核心运行时库，提供了Java编程语言大部分的特性，可以供Java API FrameWork使用。

（4）本地C/C++库（Native C/C++ Libraries）： 许多Android系统组件和服务，如ART和HAL，都是构建在本地C/C++库之上的。Android系统为这些本地库提供了相应的Java FrameWork APIs供更高层的App使用。例如，你可以通过Java OpenGL API访问OpenGL ES本地库特性。

如果开发的App需要C或者C++代码，可以使用Android NDK访问一些本地库。

（5）Java API框架（Java API FrameWork）：Java API框架提供了访问Android系统全部特性的接口，创建App只需要简单地重用这些核心的模块化系统组件和服务，包括：一个用来构建应用UI（列表、网格、文本框、按钮和内嵌Web浏览器等）的丰富的可扩展的视图系统（View System），一个提供访问非代码资源（如国际化字符串、图形和布局文件等）的资源管理器（Resource Manager），一个能让所有应用在状态栏上显示自定义弹出框的通知管理器（Notification Manager），一个管理了app生命周期并提供了通用的navigation back stack的活动管理器（Activity Manager），一个能让所有应用访问其他应用的数据和分享自己的数据的内容管理器（Content Provider）。

（6）系统应用（System Apps）：系统应用是Android自带的基于Java API FrameWork实现的一些基本应用，如邮件、日历、网络浏览器、联系人等等。
</pre>

# 2.  平台架构


# 3. 应用组件


# 4. 应用资源


# 5. 应用Manifest


# 6. 用户界面


# 7. 动画和图形


# 8. Computation

# 9. 多媒体应用


# 10. 多媒体和相机


# 11. 位置和传感器


# 12. 网络连接


# 13. 文本和输入


# 14. 数据存储


# 15. Administration


# 16. 网络应用


# 17. 最佳实践


# 参考：
1. https://developer.android.google.cn/guide/platform/index.html
