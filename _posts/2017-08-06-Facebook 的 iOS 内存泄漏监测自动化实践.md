---
layout: post
title: Facebook 的 iOS 内存泄漏监测自动化实践
author: 平谦
---

*摘要：* 原文链接 [需翻墙]：[Automatic memory leak detection on iOS](https://code.facebook.com/posts/583946315094347/automatic-memory-leak-detection-on-ios/) 内存是移动设备上的共享资源，如果一个 App 无法正确地进行内存管理的话，将会导致内存消耗殆尽，闪退以及性能的严
原文链接 [需翻墙]：[Automatic memory leak detection on iOS](https://code.facebook.com/posts/583946315094347/automatic-memory-leak-detection-on-ios/)
内存是移动设备上的共享资源，如果一个 App 无法正确地进行内存管理的话，将会导致内存消耗殆尽，闪退以及性能的严重下降。
Facebook 的 iOS 版本的许多功能模块共用了同一份内存空间，如果其中的某一个模块消耗了特别多的内存资源的话，将会对整个 App 造成严重影响。举个栗子，当某个功能模块不小心造成了内存泄漏的时候，这个情况就很有可能会发生。
在 Facebook，我们有非常多的工程师同时在一个代码仓库下进行并行开发。内存泄漏是在开发过程中难以避免会遇见的问题。当内存泄漏发生时，我们就需要快速地去发现然后修复它。
现在已经存在一些开发者工具来辅助发现内存泄漏了，但是它们的共同点是需要大量的人工操作：
打开 Xcode 并选择 build for profiling 来编译你的工程
打开 Instruments 工具
尝试在你的应用上尽可能多地重现更多的场景与行为
观察内存工具的走势图
找到内存泄漏的源头
修复它！

这样的人工排查与修复工程每次都得不断地重复操作。正因为如此，我们很难在迭代阶段早期就定位与修复内存问题。
将内存泄漏的排查过程尽可能地自动化，减少开发人员的人工干预，可以帮助我们更快地去找到内存泄漏的地方。为了解决这个问题，我们已经在内部开发了一套工具来帮助我们自动化这个排查过程，并且已经帮助我们解决了许多代码中存在的内存泄漏问题。今天，我们很高兴向大家宣布我们正式开源这套内存泄漏排查工具：[FBRetainCycleDetector](https://github.com/facebook/FBRetainCycleDetector)，[FBAllocationTracker](https://github.com/facebook/FBAllocationTracker) 和 [FBMemoryProfiler](https://github.com/facebook/FBMemoryProfiler)。
循环引用
Objective-C 使用引用计数来管理内存与释放未被引用的对象。内存中的对象 A 可以让对象 B 的引用计数加一，即 retain，来使对象 B 尽可能久地存在内存中（只要对象 A 不对它“减一”，即 release）。也就是说：对象 A 持有了对象 B 。
大多数情况下，引用计数这套机制都可以运作得很好。但是，当两个对象直接地，或者更常见的情形是通过某些对象间接地，互相持有了对方，这个时候就陷入了僵局了。这种互相持有对方的引用的现象叫做循环引用。
![](http://upload-images.jianshu.io/upload_images/3510862-bafe627271374148.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
循环引用会导致一系列的问题。最好的情况是，泄漏的对象本身就会一直长期地占用内存空间，这种情况一般不会造成太大的内存消耗。如果泄漏的对象不停地增加与积累，那么 App 中其他功能模块所能使用的内存就会减少。最坏的情况则是，内存泄漏导致了 App 需要使用的内存超出了限制，这时应用就会闪退了。
通过人工排查的手段，我们发现我们有太多因为循环引用导致的内存泄漏了。日常编码中，稍不加注意就有可能把循环引用给引入到代码里，而之后却不容易发现他们。[FBRetainCycleDetector](https://github.com/facebook/FBRetainCycleDetector) 这个工具将帮助我们把循环引用的监测变得更加简单。
在运行期监测循环引用
在 Objective-C 中检测循环引用可以抽象为在一个节点为对象，边为对象之间的引用关系的有向无环图（DAG 图）中寻找存在的环。当所有的 Objective-C 对象已经在我们的有向无环图中时，我们所需要做的就是通过深度优先搜索算法来遍历它，并找到循环节点。
[这里有个视频 - 需翻墙](https://www.facebook.com/Engineering/videos/10154016186392200/)
将循环引用的检测问题抽象为简单的数据结构算法之后，整个方案就变得非常清晰了。我们需要确认的就是我们能够在运行期找到所有的内存对象并找出他们之间所有的引用关系。对象之间的引用关系可能是弱引用，也可能是强引用。而只有对象之间的强引用才会导致循环引用。因此，我们只需要找到每个对象所存在的强引用即可。
幸运地是，Objective-C 提供给了我们非常强大的 Runtime 库，可以帮助我们在运行期获取足够的数据来构建这样一张有向无环图。
有向无环图中的节点可以是一个对象，或者是一个 block，接下来我们将分别进行讨论。
Objects（对象）
Objective-C Runtime 提供了很多工具来帮助我们在运行期获取一个对象的详细信息（也称作内省，Introspection，这是面向对象语言和环境的一个强大特性）。
我们要做的第一件事情就是获取对象中所有变量的 ivar layout。
const char *class_getIvarLayout(Class cls);const char *class_getWeakIvarLayout(Class cls);

对于一个给定的对象，它的 ivar layout 可以让我们获取到这个对象持有了多少对别的对象的引用关系。ivar layout 为我们提供了一个内存地址偏移量的“索引”，让我们能够通过对“索引”的叠加来得到它所持有的另一个对象的内存地址。OC Runtime 也给我们提供了可以获取一个对象的所有的弱引用关系的工具：weak ivar layout。我们可以假定： ivar layout 与 weak ivar layout 之间的关系链条差值即一个对象的所有强引用关系。
除此之外，还需要额外做一部分工作来支持 Objective-C++。在 Objective-C++ 中，我们可以在 structs 中定义对象，而这样的对象不会被 ivar layout 提供索引。而 OC Runtime 刚好提供了类型编码机制（Type Encoding）来帮助我们处理这个问题。对于每一个实例变量，类型编码可以告诉我们这个对象是什么样的数据类型。如果对象是个 struct，那么类型编码会告诉我们这个 struct 是由哪些字段和类型组成的。我们通过转换类型编码来发现哪些实例变量是 Objective-C 的对象，并计算内存地址的偏移量来获取所有他们指向的对象地址。
不过仍然有一些边界 case 我们不能深入地去解决。大多数是跟一些集合类型相关的，我们不得不遍历集合来获取所有的变量所持有的对象，这可能会导致一些潜在的副作用。
Blocks
Blocks 跟对象有一些不同。OC Runtime 没有提供给我们简单的获取它的 ivar layout 的方法，但是我们可以通过一些小 trick 来解决这个问题。
我们借鉴了 Mike Ash 在他的项目 [Circle](https://github.com/mikeash/Circle) 中使用的方法来处理 blocks，这个方案也启发了我们开发了 FBRetainCycleDetector。
这里我们运用到了 ABI （[application binary interface for blocks](http://clang.llvm.org/docs/Block-ABI-Apple.html)）。它告诉我们 block 在内存中是以怎样的形式存在的。如果我们知道我们在处理的引用是一个 block 的话，那么我们可以将其转化为一个假的模拟 block 的 struct 对象。在将 block 转化为 struct 之后，我们就能够知道它所持有的内存对象了。但是不幸地是，我们并不知道这些引用关系是强引用还是弱引用。
我们用了黑盒技术来解决这个问题。我们创建了一个假装是我们要排查的 block 的对象，且我们知道 block 的接口结构，以及到哪去找 block 所持有的引用。而在我们“伪造的” block 对象中，并没有实际持有引用，而是持有了“释放探测器”（release detectors）。释放探测器是一些用来监听发送给他们的内存释放消息的小对象。当一个对象要解除引用关系的时候，它会像其持有的强引用对象发送内存释放的消息。所以，我们可以在释放“伪造的” block 对象的时候，检查一下哪个探测器收到了内存释放的消息，这些收到消息的探测器，即存在强引用关系，这样就可以帮助我们找出真实的 block 对象中所持有的对象引用了。
[图片上传中。。。（2）]
自动化
这个内存检测工具在我们日常持续不断地迭代构建开发中，不停地发光发热贡献着自己的一份力。
在客户端上的自动化非常地简单。我们在自己的 App 里添加了 Retain Cycle Detector 的依赖，并周期性地扫描内存片段来检测循环引用。当然，它也并不是完璧无瑕的。当我们第一次运行 Retain Cycle Detector 的时候，我们就意识到了它是无法非常快地扫描整个内存使用的，我们需要为它提供一些筛选过后的内存对象来让它进行检测。
为了更有效地进行对象筛选，我们开发了 FBAllocationTracker。这是一个用来主动追踪所有 NSObject 的子类的内存分配和释放操作的工具。它可以在最小的性能消耗下，在给定的时间点快速获取任何类的任何实例。
在客户端上自动进行内存泄漏监测实际上就是配合使用 FBRetainCycleDetector 加定时器，再加上可以为我们筛选检测对象的 FBAllocationTracker。
现在让我们来看下后端需要做哪些特别的处理。
循环引用可以由任意数量的多个对象组成。但是当出现了一个坏链接而导致出现多个循环时，事情就变得复杂起来了。
![AB is a bad link in a cycle, and two kinds of cycles are created because of that: A-B-C-D and A-B-C-E.](http://upload-images.jianshu.io/upload_images/3510862-4d3f77eb844888e0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这会导致两个问题：
如果是因为一个坏链接导致的两个循环引用，我们并不想把他们分开标记出来
如果真的是两个循环引用问题，即便是共享了一个链接，我们也不想把他们标记在一起

因此，我们需要对循环引用进行归类，为此我们也写了一个算法来启发式地帮助我们处理这个问题：
将同一天检测到的循环引用归类
对每一个循环引用，提取出 Facebook 特有的类名
对每一个循环引用，找到其中已经被上报过的、并包含在其中的最小环
将每个循环引用添加到上述最小环所表示的组中
只对最小环进行上报

做完上述的处理之后，最后要做的事情就是找到哪个工程师改动了代码导致了内存泄漏的发生。我们通过 'git/hg blame' 配合循环引用中的部分代码，来推测出可能是某个工程师的改动导致的问题发生，并在内部的办公系统中对其发出通知，让他尽快修复这个问题。
整个自动化系统可以通过下面的图形进行表示：
![](http://upload-images.jianshu.io/upload_images/3510862-ddbd3a00cdfceb15.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
人工检测
虽然自动化的方式帮助我们简化了循环引用的排查，也减轻了我们工程师的负担，但是人工排查仍然具有不可替代的作用。为此，我们还开发了 FBMemoryProfiler 来让任何人都可以在不把 iPhone 连接到电脑的情况下，也可以当前 App 的内存使用情况。
FBMemoryProfiler 可以通过很简单的方式就添加到任何 App 中，让你具备直接在 App 中查看内存使用情况，并手动检查循环引用的能力。FBMemoryProfiler 依赖了 FBAllocationTracker 和 FBRetainCycleDetector 来实现这些功能。
[这里有个视频 - 需翻墙](https://www.facebook.com/Engineering/videos/10154016195112200/)
Generations（代）
FBMemoryProfiler 提供的另一个非常好用的功能是“代追踪”（generation tracking），类似于 Xcode 提供的 Instruments 里的代追踪的功能。“代”（Generation）可以认为是两个时间切片之间所有存在的内存对象的快照（snapshots）。
举个栗子，通过 FBMemoryProfiler 提供的 UI 你标记了一次时间点，这时分配了三个内存对象。然后你标记了另一个时间点，同时也继续分配内存对象。第一个时间点的内存中只包含了我们最开始的三个内存对象。如果其中任何一个对象被释放了，那么它就不会存在第二个时间点的内存切片中。
![](http://upload-images.jianshu.io/upload_images/3510862-6095e23ad08ac639.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当我们需要做一些重复的动作的时候，代追踪这个功能是非常有用的。举个栗子，比如我们需要反复进出某个 View Controller 的时候。我们每次都在进入 View Controller 之前标记一下内存快照，然后仔细关注一下每次内存快照都有哪些对象剩下。如果有个对象存在的时间超出了它原本的预期的话，我们就可以非常直观地从 FBMemoryProfiler 上看到。
来试试看吧
不论你是巨无霸 App 还是一个小型应用，良好的内存管理都是一个好的工程习惯。通过这些工具的帮助，我们能够更为便捷地去发现和修复内存泄漏的问题，让我们省下那些去手动检测的时间，更加聚焦在写出更好的代码上。我们也希望你能够从这些工具里得到帮助。来 Github 上给我们加个 star 吧：[FBRetainCycleDetector](https://github.com/facebook/FBRetainCycleDetector)，[FBAllocationTracker](https://github.com/facebook/FBAllocationTracker) 和 [FBMemoryProfiler](https://github.com/facebook/FBMemoryProfiler)。