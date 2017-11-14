#
1.CocoaPods的原理是将所有的依赖库都放在另一个名为Pods的项目中，然后让主项目依赖Pods项目，这样，源码管理工作都从主项目移到了Pods项目。

##
(1)Pods项目最终会编译成一个名为libPods.a的文件，主项目只需要依赖这个.a文件即可；

(2)对于资源文件，CocoaPods提供了一个名为Pods-resources.sh的bash脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中；

(3)CocoaPods通过一个名为Pods.xconfig的文件在编译时设置所有的依赖和参数。
