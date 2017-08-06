---
layout: post
title: GNU Linux和Unix Darwin BSD等
author: 平谦
---

#Unix起源
　[Linux](https://www.baidu.com/s?wd=Linux&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmW7-m10dn1NWnH7BnyPB0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHDYPjRYPHc) 的源头要追溯到最古老的UNIX。1969年，Bell实验室的Ken Thompson开始利用一台闲置的 [PDP](https://www.baidu.com/s?wd=PDP&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmW7-m10dn1NWnH7BnyPB0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHDYPjRYPHc)-7计算机开发了一种多用户，多任务操作系统。很快，Dennis Richie加入了这个项目，在他们共同努力下诞生了最早的UNIX。Richie受一个更早的项目——MULTICS的启发，将此操作系统命名为 Unix。早期UNIX是用[汇编语言](https://www.baidu.com/s?wd=%E6%B1%87%E7%BC%96%E8%AF%AD%E8%A8%80&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmW7-m10dn1NWnH7BnyPB0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHDYPjRYPHc)编写的，但其第三个版本用一种崭新的编程语言C重新设计了。C是Richie设计出来并用于编写操作系统的程序语言。通过这次重新编写，Unix得以移植到更为强大的 [DEC](https://www.baidu.com/s?wd=DEC&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmW7-m10dn1NWnH7BnyPB0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHDYPjRYPHc) [PDP](https://www.baidu.com/s?wd=PDP&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmW7-m10dn1NWnH7BnyPB0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHDYPjRYPHc)-11/45与11/70计算机上运行。后来发生的一切，正如他们所说，已经成为历史。Unix从实验室走出来并成为了操作系统的主流，现在几乎每个主要的计算机厂商都有其自有版本的Unix.

#Linux起源
Linux起源于一个学生的简单需求。Linus Torvalds,Linux的作者与主要维护者，在其上大学时所买得起的唯一软件是Minix. Minix是一个类似Unix，被广泛用来辅助教学的简单操作系统。Linus 对Minix不是很满意，于是决定自己编写软件。他以学生时代熟悉的Unix作为原型， 在一台Intel 386 [PC](https://www.baidu.com/s?wd=PC&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmW7-m10dn1NWnH7BnyPB0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHDYPjRYPHc)上开始了他的工作。他的进展很快，受工作成绩的鼓舞，他将这项成果通过互连网与其他同学共享，主要用于学术领域。有人看到了这个软件并开始分发。每当出现新问题时，有人会立刻找到解决办法并加入其中，很快的， Linux成为了一个操作系统。值得注意的是
>Linux并没有包括Unix源码。它是按照公开的POSIX标准重新编写的。Linux大量使用了由麻省剑桥免费软件基金的GNU软件，同时Linux自身也是用它们构造而成。

#什么是GNU
GNU 是一个由 [自由软件](http://www.gnu.org/philosophy/free-sw.html)— 构成的操作系统; 它尊重其使用者的自由。GNU 的开发使你能够使用电脑而无需安装可能会践踏你自由的软件。
>GNU 是一个类 Unix 操作系统。它是由多个应用程序、系统库、开发工具乃至游戏构成的程序集合。GNU 的开发始于 1984 年 1 月，称为 GNU 工程。GNU 的许多程序在 GNU 工程下发布；我们称之为 [GNU 软件包](http://www.gnu.org/software/)。
“GNU” 这个名字是 “GNU's Not Unix” 的递归首字母缩写词。[“GNU” 的发音为 *g'noo*](http://www.gnu.org/pronunciation/pronunciation.html)，只有一个音节，发音很像 “grew”，但需要把其中的 *r* 音替换为 *n* 音。
类 Unix 操作系统中用于资源分配和硬件管理的程序称为 “内核”。GNU 所用的典型内核是 Linux。该组合叫做 [**GNU/Linux 操作系统**](http://www.gnu.org/gnu/linux-and-gnu.html)。GNU/Linux 为几百万用户所使用，然而许多人 [错误地称之为 “Linux”](http://www.gnu.org/gnu/gnu-linux-faq.html)。
GNU 自己的内核，[The Hurd](http://www.gnu.org/software/hurd/hurd.html)，开始于 1990 年（早于 Linux）。志愿者们仍在继续开发 Hurd，因为它是一个有趣的技术项目。


##什么是自由软件
**自由软件意味着使用者有运行、复制、发布、研究、修改和改进该软件的自由。**
自由软件是权利问题，不是价格问题。要理解这个概念，你应该考虑 “自由” 是 “言论自由” 中的“自由”；而不是 “免费啤酒” 中的“免费”。
更精确地说，自由软件赋予软件使用者 [四项基本自由](http://www.gnu.org/philosophy/free-sw.html)：
 - 不论目的为何，有运行该软件的自由（自由之零）。
 - 有研究该软件如何运行，以及按需改写该软件的自由（自由之一）。取得该软件源代码为达成此目的之前提。
 - 有重新发布拷贝的自由，这样你可以借此来敦亲睦邻（自由之二）。
 - 有改进该软件，以及向公众发布改进的自由，这样整个社群都可受惠（自由之三）。取得该软件源码为达成此目的之前提。

#关于苹果系统
>![查看Unix系统版本](http://upload-images.jianshu.io/upload_images/3510862-205e460d6966de66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在命令行输入uname 得到的结果如下,
`Darwin pingqiandeMacBook-Pro.local 15.6.0 Darwin Kernel Version 15.6.0: Thu Sep  1 15:01:16 PDT 2016; root:xnu-3248.60.11~2/RELEASE_X86_64 x86_64`

可见苹果系统使用的是Darwin内核.而darwin是完全基于BSD标准构建.

Mac OS X其实也采用的是UNIX内核，图形的分层结构，但具体实现有很大的区别。　　
 - UNIX使用X Window（[麻省理工](https://www.baidu.com/s?wd=%E9%BA%BB%E7%9C%81%E7%90%86%E5%B7%A5&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YdPW--nvckuHNBuyNhuAF-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPjRdrjRLPHTYPH0YnW0LrHmd)开发）构建图形环境，X Window相当于在内核外跑的一个应用，它包括X Server与X Client两个部分，其中X Server位于底层，与[操作系统](https://www.baidu.com/s?wd=%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YdPW--nvckuHNBuyNhuAF-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPjRdrjRLPHTYPH0YnW0LrHmd)内核通讯，它主要处理输入输出信息并维护相关资源；X Client则面向用户，负责提供一个完整的GUI界面，[Linux系统](https://www.baidu.com/s?wd=Linux%E7%B3%BB%E7%BB%9F&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YdPW--nvckuHNBuyNhuAF-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPjRdrjRLPHTYPH0YnW0LrHmd)中非常流行的GNOME和KDE桌面环境就是最常见，最著名的X Client。　　
 - 苹果没用使用X Window，而是开发一套名为Aqua的GUI，这也是Mac OS X与其他UNIX版本根本性的区别。至于内核，Mac OS X基于“Darwin”构建，Darwin本身就是一个完整的UNIX/[BSD系统](https://www.baidu.com/s?wd=BSD%E7%B3%BB%E7%BB%9F&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YdPW--nvckuHNBuyNhuAF-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPjRdrjRLPHTYPH0YnW0LrHmd)，具有UNIX体系惯有的高度可靠性和健壮性。Darwin项目的创始公司是[苹果公司](https://www.baidu.com/s?wd=%E8%8B%B9%E6%9E%9C%E5%85%AC%E5%8F%B8&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YdPW--nvckuHNBuyNhuAF-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPjRdrjRLPHTYPH0YnW0LrHmd)，但Darwin完全是基于开源项目FreeBSD 5.0和Mach 3.0构建。

#Linux的派生和Unix的派生
 - BSD曾经被认为是UNIX的一支——"BSD UNIX", 因为它和AT&T UNIX操作系统共享基础代码和设计。在20世纪80年代，衍生出了许多变形的UNIX授权软件。比较著名的如DEC的Ultrix及Sun公司的SunOS。1990年代，BSD很大程度上被System V4.x版以及OSF/1系统所取代，晚期BSD版本为几个开源软件开发提供了平台并且一直沿用至今。今天，“BSD”并不特指任何一个BSD衍生版本，而是类UNIX操作系统中的一个分支的总称。
 - BSD是Unix的一个重要分支，本身就是Unix，Linux不是Unix,而只是Unix-like操作系统。BSD采用BSD协议发布其2进制文件和源码，Linux则采用GPL协议。　　由于法律纠纷，等诸多原因，使同为自由软件的BSD没能像Linux一样广泛传开来。

>主流的Linux发行版　
Ubuntu， Debian [GNU/Linux](https://www.baidu.com/s?wd=GNU%2FLinux&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4njmYPycdnHTdP1ubmWfz0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3En1nsP10srHcd) ，Fedora ，Gentoo ，MandrivaLinux ，PCLinuxOS，Slackware Linux ，openSUSE，ArchLinux，Puppylinux，Mint, CentOS,Red Hat等。