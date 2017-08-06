---
layout: post
title: charset encode 的原理与区别
author: 平谦
---

#charset： 

---

>网页的一般字符集规定:
<meta http-equiv="content-type" content="text/html; charset=UTF-8" />

 - [UTF-8](http://baike.baidu.com/item/UTF-8): 是国际字符编码，也就是独立于任何一种语言，任何语言都可以使用。对于中文使用3个字节,对于英文使用一个字节.UTF-8包含全世界所有国家需要用到的字符。
 - GBK:每个字符占用2个字节.GBK是在国家标准GB2312基础上扩容后兼容GB2312的标准.GBK是国家编码，通用性比UTF8差，不过UTF8占用的数据库比GBK大。
 - GB2312:只支持简体，共7445个字符。GBK有21886个汉字字符，支持繁体中文，GB18030就更多，甚至支持一些少数民族文字。

#URL编码： 

---

>HTML form提交、JS里的encodeURIComponent函数

- form提交（application/x-www-form-urlencoded)
    - 在gbk.html页面【土豆】会变成【%CD%C1%B6%B9】，在utf8.html页面【土豆】会变成【%E5%9C%9F%E8%B1%86】。`重要:form提交会把空格替换成加号（+）,有且仅有这一种情况下,空格会替换成+,当我们在使用encodeURIComponent()函数的时候,会将空格替换成%20`.
- encodeURIComponent函数：
 - 不管页面的charset，都按UTF-8来处理，所以在gbk.html和utf8.html页面上执行encodeURIComponent("土豆")之后都得到 【%E5%9C%9F%E8%B1%86】。encodeURIComponent完全符合RFC1738，会把空格替换成【%20】.

#浏览器地址栏行为分析
 ---
 ##1. 打开encode后的URL：
   GBK页面：
打开 http://localhost/gbk.html?kw=%CD%C1%B6%B9 ：
所有浏览器的地址栏直接显示 http://localhost/gbk.html?kw=%CD%C1%B6%B9。Opera比较特殊，默认隐藏GET参数部分，焦点移到地址栏时显示完整URL。
 
  UTF-8页面：
打开 http://localhost/utf8.html?kw=%E5%9C%9F%E8%B1%86 ：IE、Opera：直接显示URL，http://localhost/utf8.html?kw=%E5%9C%9F%E8%B1%86 Firefox、Chrome：把URL解码后显示， http://localhost/utf8.html?kw=土豆

##2. 打开encode前的URL：
 
>在浏览器地址栏直接输入带中文参数的URL按回车，有些浏览器会对中文进行URL编码，这个行为在不同浏览器不太一致。
 
打开 http://localhost/gbk.html?kw=土豆 ：
 
IE：无处理，发送 http://localhost/gbk.html?kw=土豆
Opera：URL编码， http://localhost/gbk.html?kw= %CD%C1%B6%B9
Firefox、Chrome：UTF8 + URL编码， http://localhost/gbk.html?kw=%E5%9C%9F%E8%B1%86
 
>URL里带中文时需要看具体操作系统和浏览器，没有统一的标准。IE根据OS字符编码直接发送中文，Opera也是根据OS编码发送中文，只不过多一个URL编码处理。Firefox和Chrome一律按UTF-8字符处理，并进行URL编码。同事反映Firefox中文版是按OS编码发送中文，这个和Firefox本身的URL解码一起会导致BUG。

#总结
 ---
当我们在用表单发送数据包含中文的时候,我们往往要使用到content-type : application/x-www-form-urlencoded ,这个时候中文字符会被按照页面的编码方式编码,服务端需要对该编码方式进行解码.(注意这种编码方式数据中的+会被decode为空格,确认一下是否符合你的需要.)
如果我们在使用的过程中使用encodeURIComponent方法对数据进行了encode 那么数据就已经被按照utf8的方法encode了,服务端需要使用utf8的方式进行解码.