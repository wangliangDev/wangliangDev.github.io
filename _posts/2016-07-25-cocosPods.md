---
layout: post
title: 2016安装cocoaPods
category: 技术
comments: true
---

作者：skytoup（Github）

aaaaaaa
#####CocoaPods简介

CocoaPods是一个管理Swift和Objective-C的Cocoa项目的依赖工具。它现在有超过一万八千多个库，可以优雅地帮助你扩展你的项目。简单的说，就是替你管理Swift和Objective-C的Cocoa项目的第三方库引入。

官网地址: [https://cocoapods.org/](https://cocoapods.org/)

#####安装

Mac上面本来就自带了ruby，所有就不用自己安装了(除非你卸载了)。
打开Terminal(终端)，输入以下命令(第二个命令可能会需要稍等一会儿)

```
gem sources --remove https://rubygems.org
gem source -a https://gems.ruby-china.org

```

第一个命令是移除官方源，因为在不翻墙的情况下，使用起来比较慢；第二个命令是添加ruby-china的RubyGems镜像(很多旧教程都是说使用taobao的gem源，但是taobao的gem源已经停止维护了，原文:https://ruby-china.org/topics/29250)。

接下来运行一个命令查看是否成功添加了ruby-china的gem源:

`gem source`

出现下图这样子，则代表成功添加~

![](http://cc.cocimg.com/api/uploads/20160601/1464751049903867.png)

然后就可以开始真正安装CocoaPods了，输入一下命令:


`sudo gem install cocoapods`

等一会儿就能安装完成~~~

安装结束后，需要运行一下命令初始化CocoaPods:

`pod setup`

没有什么错误的话，就算了安装结束了。

基本使用

打开Terminal(终端)，cd到你的Project目录，输入一下命令:

`pod init`

运行结束后，该目录下，会生成了一个Podfile文件

使用文本编辑器(vim、Sublime Text2、等等…)打开它(Podfile)，大概会看到以下的东西

```
platform :ios, 'xxx' # 目标平台及其版本use_frameworks! # swift项目需要这句话，是Objective-C项目的话，请在前面加个`#`注释掉target 'xxxx' do
# 在这里添加你的依赖库说明，如pod xxx
pod 'Alamofire', '~> 3.1’ # 例如这是引入Alamofire这个第三方库
end
```

编辑完Podfile后，使用Terminal(终端)输入其中一个命令(需要cd到项目的根目录，即Podfile所在目录):


`pod install --no-repo-updateorpod install`

第一个命令是不更新本地库信息进行安装，速度会快一点，毕竟不需要更新。但是会有一点点问题，当有一个新的库发布的时候，就会无法安装成功。如果不嫌麻烦，可以定时执行以下命令更新CocoaPods的库，然后就可以在一段时间使用以上的第一个命令进行安装:

`pod repo update`

安装完成之后，打开项目就需要打开xxx.xcworkspace，而不是xxx.xcodeproj了

如果在安装之后，修改了Podfile文件，可以执行以下的其中一个命令进行库的更新(两个命令的区别和上面说的一样):



`pod update --no-repo-updateorpod update`

安装CocoaPods的可能失败原因

gem过旧，使用以下命令更新一下，再进行安装(先切换到了ruby-china的gem源再运行一下命令更新):


`sudo gem update`












