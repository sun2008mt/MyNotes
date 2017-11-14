1.CocoaPods原理
---
CocoaPods的原理是将所有的依赖库都放在另一个名为Pods的项目中，然后让主项目依赖Pods项目，这样，源码管理工作都从主项目移到了Pods项目
##

(1)Pods项目最终会编译成一个名为libPods.a的文件，主项目只需要依赖这个.a文件即可；

(2)对于资源文件，CocoaPods提供了一个名为Pods-resources.sh的bash脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中；

(3)CocoaPods通过一个名为Pods.xconfig的文件在编译时设置所有的依赖和参数。

2.自动引用计数（ARC，Automatic Reference Count）
---
当一个新对象被创建时，它的引用计数为1，当有一个新的指针指向这个对象时，引用计数加1；当该指针不再指向这个对象时，引用计数减1；当引用计数变为0时，表明对象不再被任何指针指向，因而可以将对象销毁，回收内存。
##

（1）循环引用（reference cycles），相互依赖时产生
（2）弱引用（weak reference）虽然持有对象，但并不增加引用计数

3.GCD，Grand Gentral Dispatch
---

4.UIWindow
---
UIWindow是其他UIView的容器（UIWindow继承自UIView）
