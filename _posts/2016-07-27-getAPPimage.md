---
layout: post
title: 教你快速拿到iOS应用中所有图片资源
mage: /assets/images/markdown.jpg
headerImage: false
tag:
- IOS技术
blog: true

author: ""
description: ""

---

####本文作者  Monkey_ALin （转自简书）

原文链接：[http://www.jianshu.com/p/78dea31f2109](http://www.jianshu.com/p/78dea31f2109)



### 前言
最近很多朋友问我, 我高仿了那么多项目, 图片资源和其他资源文件是怎么拿到的. 我总是神秘的回答: 山人自有妙计. 今天, 我就一步一步教大家拿到一个iOS应用里面的所有资源.

### 常识
Images.xcassets这个文件夹大家都不陌生. 它在编译的时候, 会被打包为Assets.car. 而这个Assets.car就变成了我们获取图片资源的拦路虎.


```java
iOS APP中所有资源 =  Assets.car +  .api文件解压

```

我们以微信为例

获取api文件里面的图片

- A. 打开你Mac上的`iTunes` 操作如下

![](http://upload-images.jianshu.io/upload_images/1038348-92d64cec7f4510c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<center> itunes操作</center>


- B. 点击`我的应用`, 找到刚下载好的应用, 右击`在finder中显示`


![](http://upload-images.jianshu.io/upload_images/1038348-68af83859ac6b609.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- C. 按Enter(回车键), 修改微信ipa文件的后缀为.zip, 即把微信 6.3.22.ipa变成微信 6.3.22.zip, 此处会有一个提示, 问你是否确定修改扩展名, 点击使用.zip即可

- D. 直接双击zip进行解压, 打开解压好的文件夹, 进入Payload文件夹


![](http://upload-images.jianshu.io/upload_images/1038348-276969f91e487bfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- E. 此时, 就拿到了大多数的资源. 包括css, js, 图片, MP3/4, 字体,xib等等资源

### 取Assets.car中的资源
这儿我们使用一个工具即可, 具体出处我忘了(如果你是软件作者, 可以联系我添加相关信息)下面附下载地址:

```
百度云
下载地址: http://pan.baidu.com/s/1kUVAT7p
提取密码: qrt5
我们在上面的E步骤所在的文件夹处搜索Assets.car即可
```

打开我云盘中提供的工具, 直接将Assets.car拖入其中即可, 对, 拖进去就行了
点击start, 完成后, 点击Output Dir即可

![](http://upload-images.jianshu.io/upload_images/1038348-6b2defdfc99ca86e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- A.有一些应用, 是没有Assets.car的, 直接解压ipa文件即可获取所有资源
- B. 里面有多少个Assets.car, 就获取多少个Assets.car的资源, 最后汇总.
- C. iOS APP中所有资源 =  Assets.car +  .api文件解压











