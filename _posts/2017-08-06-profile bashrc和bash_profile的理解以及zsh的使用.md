---
layout: post
title: ~/.profile ~/.bashrc和~./bash_profile的理解以及zsh的使用
author: 平谦
---

#.bashrc - The individual per-interactive-shell startup file.
这个文件主要保存个人的一些个性化设置，如命令别名、路径等。定义了路径，语言，命令别名（使用rm删除命令时总是加上-i参数需要用户确认，使用ls命令列出文件列表时加上颜色显示）。
每次修改.bashrc后，使用source ~/.bashrc（或者 . ~/.bashrc）就可以立刻加载修改后的设置，使之生效。
一般会在.bash_profile文件中显式调用.bashrc。
登陆linux启动bash时首先会去读取~/.bash_profile文件，这样~/.bashrc也就得到执行了，你的个性化设置也就生效了

#全局profile和用户下的~/.profile
用户可以在Profile文件中加入环境变量，比如ORACLE_HOME,HOME...这样重新登录之后，这些环境变量都会得以设置，不用每次都手工设置。
Unix/Linux有两个profile文件

1./etc/profile:是全局profile文件，设置后会影响到所有用户
2./home/username/.profile或.bash_profile是针对特定用户的，可以针对用户，来配置自己的环境变量。
注意:profile是unix上才有的;bash_profile是Linux下有的(Linux下，用户目录没有.profile文件)
     /home/username/.profile或.bash_profile，都是隐藏文件，需要使用ls -a才能看到。

#Bash登陆(login)的时候，Profile执行的顺序
1)先执行全局Profile, /etc/profile
2) 接着bash会检查使用者的HOME目录中，是否有 .bash_profile 或者 .bash_login或者 .profile，若有，则会执行其中一个，执行顺序为：
    .bash_profile 最优先 > .bash_login其次 > .profile 最后

#实战 zsh之oh-my-zsh
>所有unix系统分支下在unix 内核的操作系统中,当然现在衍生出好多分支,linux ,OS X 都算.
shell 就算和上面这些系统内核指令打交道的一座桥梁,我们通过键盘输入一种自己容易记忆识别的符号标识(shell 命令)
然后 shell 解析这种命令再反馈给内核去执行一系列操作.
其实 zsh 也是一种 shell ,但是并不是我们系统默认的 shell ,unix 衍生系统的默认shell 都是 bash
以mac 为例,我们看下系统内置了几种shell
![OS X内置shell](http://upload-images.jianshu.io/upload_images/3510862-f8f0bd0f0a0c9e85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将bash切换为zsh :`chsh -s /bin/zsh`
zsh的配置文件不再是~/.zsh_profile去调用~/.zshrc,而是直接就是~/.zshrc就可以了.所以对于zsh的一切设置,直接去~/.zshrc中设置.
详细的配置教程:[传送门](http://www.cnblogs.com/ma6174/archive/2012/05/08/2490921.html)

##什么是oh-my-zsh ?
oh-my-zsh 是zsh的一个配置开源配置文件,因为手动配置zsh太麻烦.
接下来我们需要下载 oh-my-zsh 项目来帮我们配置 zsh
wget 自动安装
`wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`
安装 oh-my-zsh 时,它自动读取你的环境变量并且自动帮 zsh 进行设置.
所以这时的zsh 基本已经配置完成,你需要一行命令就可以切换到 zsh 模式.

##如何定制化zsh配置
目前对于zsh的配置仅仅是支撑在 oh-my-zsh 的配置基础上做一点点小小改动,还不是特别深入了解.

zsh 的配置文件也在用户目录下 .zshrc 隐藏文件

##编辑配置文件
nano .zshrc
每一行的配置前面都有#号,如果想要配置生效,去掉 #号即可.下面只罗列几个可能用到的,大部分的我还没有去琢磨.