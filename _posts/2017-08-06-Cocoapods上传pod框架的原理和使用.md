---
layout: post
title: Cocoapods上传pod框架的原理和使用
author: 平谦
---

# 原理介绍
1. cocoapods是一个ios的包管理工具,基于ruby,安装使用命令`sudo gem install cocoapods`.
- cocoapods的描述文件为一个`${项目名}.podspec `的文件,通过该描述文件表征你的项目地址,项目使用的平台和版本等信息.
- cocoapods远端库仅仅只是维护所有的podspec文件,而podspec文件的正确性,需要在本地通过pod lib lint 命令来校验 校验成功才可以上传到远端master库.
- cocoapods对代码的版本控制基于`git仓库的地址`+`tag标签`,也就是说pod仓库的版本号要跟git仓库的tag号码一致.

#操作流程
#### 没有仓库
>如果用户已经有生成好的源代码文件,在源代码文件夹下使用命令`pod lib create ${项目名}`,此命令会给你生成一个默认的"模板"仓库(脚手架)和podspec文件,按照此文件去生成和提交你的三方库源代码和上传到pod仓库.

#### 已有仓库

>如果你已经有了仓库并且已经写好的自己的源代码,那么就不能使用pod 提供的脚手架再去重新生成项目,这个时候你只需要添加一个描述文件`${项目名}.podspec`文件即可,最后依照此描述文件进行代码上传.

1. 在你的项目目录下使用`pod spec create ${项目名}` 来创建一个官方的podspec描述文件.
>Pod::Spec.new do |s|
s.name = “项目名字”
s.version ="版本号"
s.summary = "项目介绍"
s.homepage = "你的主页地址"
s.license = "MIT" //这里就选这个许可证
s.author ={"你的名字" => "邮箱"}
s.platform =iOS,"最低支持的系统"
s.source ={git =>"项目git地址",tag =>"tag号"}
s.source_files = "text/*/.(h.m)" //这里是项目路径要传的文件
s.framework ="UIKit" //依赖的系统库 可以依赖多个
// 若需要依赖其他开源的第三方库 可以写成下列形式 若需要有多个就复制下面代码 添加第三方库
// s.dependency "AFNetWorking"
// 如果需要配置依赖系统库
// s.framework = 'SomeFramework'// 设置依赖的系统库名称
// s.frameworks = 'SomeFramework', 'AnotherFramework'//设置多个系统库名称
// 需要依赖系统的library
s.library = 'iconv'// 设置只依赖一个系统的library
s.libraries = 'iconv', 'xml2' // 设置依赖多个系统的library
// 这里是工程配置，这样使用者就不需要手动处理，由pod自动处理了
s.xcconfig = {'HEADER_SEARCH_PATHS' =>'$(SDKROOT)/usr/include/libxml2'}


2.对此描述文件的内容进行校验,按照提示修改错误.使用`pod lib lint ${项目名}.podspec --allow-warnings --verbose`进行校验,直到最后没有error( `passed validation` )为止.
> 关于`pod lib lint ${项目名}.podspec --allow-warnings --verbose`的实现细节探究:
在调用lint方法的时候,读取的是本地的podspec文件,但是拉取的代码,是podspec文件中的`s.source`描述的远端git仓库代码.所以podspec文件传不传到git仓库本身没有关系,因为读取的是本地的.
- 描述文件:
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3510862-1f55db0dc797b3b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 命令执行具体细节
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3510862-dc4d83600937cf5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**疑问**:描述文件中的tag设置为998,但命令执行的细节上使用的是--branch而不是tag?
当给代码的有相应的tag的时候,就会去git拉tag对应的代码,如果没有tag,就会拉对应名称的branch,如果同时又branch和tag的话,优先branch.如果都没有才会报错.


3.至此为止你的项目和你的podspec文件已经生成完毕,下面我们需要让pod的版本号 和我们当前代码的tag号保持一致,这样cocoapods才可以做好版本管理.(cocoapods就是通过git的tag号进行自己的版本控制的.)
>git add .
git commit -m "添加pod版本号${version}"
git tag 0.0.1 // tag 和上面你设定的一样 一般tag号和版本号一致
git push --tags // 本地tag push到远端
git push origin master // 本地代码push到远端
git对tag和branch的管理: [传送门](http://zengrong.net/post/1746.htm)

4.最后一切验证通过之后 将podpsec文件推到pod库中.
推送之前可以手动更新一下本地pod仓库`pod repo update`(可选)
>`pod trunk push "${项目名}".podspec --allow-warnings --verbose`

5.需要一段时间,过一段时间之后,去官方库里面搜你的项目名就可以了! [传送门](https://cocoapods.org/)

---
##常用命令解读
1.pod spec lint
 -  --verbose:打印详细的流程
 - --allow-warnings:允许warnings存在,不添加此项会导致只要有warning就会编译不通过
 - --skip-import-validation  Lint skips validating that the pod can be imported/skip linking a pod during lint
 - --use-libraries :If it needs to be a static library, lint with --use-libraries./ pod trunk push has the --use-libraries flag.[参考](https://github.com/CocoaPods/CocoaPods/issues/5213)

---
##常见问题
1.pods文件是否需要先上传到仓库之后再使用pod spec lint ..?
答案: 不需要 podspec文件可以独立存在,不需要上传到远端,文件中的s.version跟s. source中的git仓库必须有同名的branch或者tag.这样在执行lint的时候才能下载到对应的源代码进行编译校验.(.podspec文件往往会跟项目代码放到同级目录下并且保存到git仓库,但是不是必须.尤其是在调试lint的时候,不需要调试一次push一次.)

2.需要让编译支持MRC的类.
># 设置需要MRC编译的文件
  mrr_files = [
    'VZInspector/toolbox/mermoryProfile/vendor/allocationTrack/NSObject+VZAllocationTracker.mm',
    'VZInspector/toolbox/mermoryProfile/vendor/allocationTrack/VZAllocationTrackerNSZombieSupport.mm',
    'VZInspector/toolbox/mermoryProfile/vendor/Associations/VZAssociationManager.mm',
    'VZInspector/toolbox/mermoryProfile/vendor/Layout/Blocks/VZBlockStrongLayout.m',
    'VZInspector/toolbox/mermoryProfile/vendor/Layout/Blocks/VZBlockStrongRelationDetector.m',
    'VZInspector/toolbox/mermoryProfile/vendor/Layout/Classes/VZClassStrongLayoutHelpers.m'
  ]
  files = Pathname.glob("VZInspector/**/*")
  files = files.map {|file| file.to_path}
  files = files.reject {|file| mrr_files.include?(file)}
  s.requires_arc = files

3.cannot create __weak reference in file using manual reference counting 解决方案.
>配置文件添加:
s.xcconfig = {
    'CLANG_ENABLE_OBJC_WEAK' => 'YES'
  }
参考:[传送门](http://stackoverflow.com/questions/36147625/xcode-7-3-cannot-create-weak-reference-in-file-using-manual-reference-counting
)

4.头文件中含有C++文件,报错类似于`error: unknown type name 'namespace'`
>首先   s.libraries     =  "c++"
其次在lint的时候加上参数 --use-libraries.
参考:[传送门](https://github.com/CocoaPods/CocoaPods/issues/5152)

---
官方文档: [传送门](https://guides.cocoapods.org/syntax/podspec.html#specification)