---
layout: post
title: 编译原理之extern static以及extern "C"
author: 平谦
---

在iOS项目中我们经常会用到很多C语言的方法 比方说
UIGraphicsGetCurrentContext()来获取当前绘画的上下文.
当我们在OC的文件中去自定义一个C方法的时候,我们就需要用到extern关键字
具体的概念和写法这里已经写得很清楚了:
//TODO 以后有时间再来补充.
http://www.cnblogs.com/liangxiaxu/archive/2012/09/02/2667730.html