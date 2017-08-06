---
layout: post
title: ios runloop学习
author: 平谦
---

(官方文档) Threading Programming Guide  
https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW23

[视频]iOS线下分享《RunLoop》by 孙源@sunnyxx 
http://v.youku.com/v_show/id_XODgxODkzODI0.html

[深度网文]深入理解runloop
http://blog.ibireme.com/2015/05/18/runloop/

CFRunLoop 源码以及阅读
https://github.com/sunyawang/RunLoop/blob/master/CFRunLoop.c

牛人博客
http://yangchao0033.github.io/blog/2016/01/06/runloopshen-du-tan-jiu/


---

Mach port :一个轻量级的进程间通讯的方式  进程之间通过port通信。NSPort是对这个的封装。

UIKit通过RunLoopObserver在RunLoop两次Sleep间对AutoreleasePool进行Pop和Push 
将这次Loop中产生的Autorelease对象释放。


CFRunLoopMode
Mode属性是一个数组

RunLoop在同一时间段只能且必须在一种特定的Mode下Run
更换Mode的时候，需要停止当前Loop（exit），然后重启Loop


NSDefaultRunLoopMode 空闲mode  点击事件 普通回调走这个。

UITrackingRunLoopMode 滑动scrollView时

UIInitializingRunLoopMode  私有Mode App启动时

NSRunLoopCommonModes  一个Mode集合 默认情况下包含NSDefaultRunLoopMode|UITrackingRunLoopMode这两个mode

---

NSTimer和GCD的timer是没有关系的。GCD的dispatch_after方法自己维护了一个timer 只是调用点最后会回到runloop里面。
runloop的timer是内核维护的。

NSRunLoop currentRunloop方法本身就是创建runloop的方法。在子线程需要自己起runloop


Runloop自己的Mode切换
PushrunloopMode
静默状态下
 NSDefaultRunLoopMode
滑动状态下 
UITrackingRunLoopMode
结束掉一个runloop

当滑动结束 到它自己停止之间 停止滑动最后减速的过程 被CADisplayLink  添加了一个定时器timer  自己做了个减速的动画。（didenddelecelating）

runloop与gcd的关系（本来二者是两个独立的东西）
GCD中dispatch到main queue的block被分发到mainRunloop执行。
GCD自己维护了一个线程池 如果使用 如果你是子线程 GCD自己分发一个线程就好了 但是如果是GCD调到主线程 那么就要CGD跟runloop协作完成了。

pushrunloopmode  poprunloopmode 

runloop的挂起与唤醒
- 指定用于唤醒的mach_port端口，就可以去挂起了
- 调用mach_msg监听唤醒端口，被唤醒之前，系统内核将这个线程挂起，停留在mach_msg_trap状态（挂起就是mach_msg_trap状态）
- 由另一个线程（或者另一个进程中的某个线程，反正不是自己）想内核发送这个端口的msg之后，trap状态被唤醒，Runloop继续开始干活。

runloop中设置了一个gcd的timer

伪代码显示runloop执行顺序。
1.最开始，while循环之前，需要给一个过期的时间，GCD的timer帮他去监测的。就是最后的timeout参数。
2.然后进入do...while循环。
2.1 告诉观察者 我要跑timer了 我要跑sources了
2.2 跑blocks和source0（不太明白）
2.3 问一下GCD有没有dispatch_main_queue()的事情让我干。
2.4 告诉观察者 我要挂起了。代码会卡在这。
（
这里面的最核心。
这里面使用了mach_msg_trap使得进程挂起。
当接收到mach_msg的时候被唤醒。
）
2.5 当有了返回值，可以获得wakeUpPort
2.6 告诉观察者我被唤醒了。
2.7 对port的类型进行判断和执行。
![image.png](http://upload-images.jianshu.io/upload_images/3510862-f387e5f92746deb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


AFN中对于runloop的应用。
这是一个创建常驻服务线程的很好的办法。平时啥也不干 当需要的时候给这个线程做事情玩。

![image.png](http://upload-images.jianshu.io/upload_images/3510862-956638347d9e5f0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当tableview在滑动的时候，禁止设置图片的时候 ，用runloop来执行。把它放到defaultmode里面去，这样滑动的时候这个方法就不会被执行。
![image.png](http://upload-images.jianshu.io/upload_images/3510862-091b53b5425640ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


app要挂的时候，如果是bad_access就直接挂了 如果是发出signal的那种形式的话，正常情况下会干掉runloop，但是可以接起这个signal，然后让runloop重启。
![image.png](http://upload-images.jianshu.io/upload_images/3510862-68b13532b5506651.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)