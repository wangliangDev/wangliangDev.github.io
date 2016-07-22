---
layout: post
title: iOS图片加载框架－SDWebImage解读
category: 技术
comments: true
---


####本文转自简书 作者  junbinchen 

在iOS的图片加载框架中，SDWebImage可谓是占据大半壁江山。它支持从网络中下载且缓存图片，并设置图片到对应的UIImageView控件或者UIButton控件。在项目中使用SDWebImage来管理图片加载相关操作可以极大地提高开发效率，让我们更加专注于业务逻辑实现。

SDWebImage 概论

1.提供了一个UIImageView的category用来加载网络图片并且对网络图片的缓存进行管理

2.采用异步方式来下载网络图片

3.采用异步方式，使用memory＋disk来缓存网络图片，自动管理缓存。

4.支持GIF动画

5.支持WebP格式

6.同一个URL的网络图片不会被重复下载

7.失效的URL不会被无限重试

8.耗时操作都在子线程，确保不会阻塞主线程

9.使用GCD和ARC

10.支持Arm64

<br/>
<br/>
###SDWebImage 使用
1.使用IImageView+WebCache category来加载UITableView中cell的图片

`
[cell.imageView sd_setImageWithURL:[NSURL URLWithString:@"http://www.domain.com/path/to/image.jpg"] placeholderImage:[UIImage imageNamed:@"placeholder.png"]];
`
2.使用Blocks,采用这个方案可以在网络图片加载过程中得知图片的下载进度和图片加载成功与否

`
[cell.imageView sd_setImageWithURL:[NSURL URLWithString:@"http://www.domain.com/path/to/image.jpg"] placeholderImage:[UIImage imageNamed:@"placeholder.png"] completed:^(UIImage image, NSError error, SDImageCacheType cacheType, NSURL *imageURL) { ... completion code here ... }];
`


3.使用SDWebImageManager,SDWebImageManager为UIImageView+WebCache category的实现提供接口。

`
SDWebImageManager manager = [SDWebImageManager sharedManager] ;[manager downloadImageWithURL:imageURL options:0 progress:^(NSInteger   receivedSize, NSInteger expectedSize) { // progression tracking code }   completed:^(UIImage image, NSError error, SDImageCacheType cacheType,   BOOL finished, NSURL imageURL) { if (image) { // do something with image } }];
`

4.加载图片还有使用SDWebImageDownloader和SDImageCache方式，但那个并不是我们经常用到的。基本上面所讲的3个方法都能满足需求。

<br/>
<br/>

###SDWebImage 流程

![](http://upload-images.jianshu.io/upload_images/656644-7dfe370a86e157e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


<br/>
<br/>

###SDWebImage 接口
SDWebImage是一个成熟而且比较庞大的框架，但是在使用过程中并不需要太多的接口,这算是一种代码封装程度的体现。这里就介绍比较常用的几个接口。


- 1.给UIImageView设置图片的接口，SDWebImage有提供多个给UIImageView设置图片的接口，最终所有的接口都会调用下图的这个接口，这是大多数框架的做法。

![](http://upload-images.jianshu.io/upload_images/656644-0ba1638e8a0a7286.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 2.获取SDWebImage的磁盘缓存大小,在项目中有时候会需要统计应用的磁盘缓存内容大小，那么获取图片的缓存大小就是使用这个接口来实现

`
[SDImageCache sharedImageCache] getSize];
`

- 3.清理内存缓存，清理内存中缓存的图片资源，释放内存资源。

`[[SDImageCache sharedImageCache] clearMemory];
`

- 4.有了清理内存缓存，自然也有清理磁盘缓存的接口

`
[[SDImageCache sharedImageCache] clearDisk];
`


<br/>
<br/>

###SDWebImage 解析

解析主要围绕着SDWebImage的图片加载流程来分析，介绍SDWebImage这个框架加载图片过程中的一些处理方法和设计思路。

- 1.给UIImageView设置图片，然后SDWebImage调用这个最终的图片加载方法


![](http://upload-images.jianshu.io/upload_images/656644-2d901e0dff51aa2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 2.开始加载之前图片先取消对应的UIImageView先前的图片下载操作。试想，如果我们给UIImageView设置了一张新的图片，那么我们还会在意该UIImageVIew先前是要加载哪一张图片么？应该是不在意的吧！那是不是应该尝试把该UIImageView先前的加载图片相关操作给取消掉呢。


`[self sd_cancelCurrentImageLoad]
`

<br/>


![](http://upload-images.jianshu.io/upload_images/656644-11f5f798d3eea349.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


该方法经过周转，最后调用了以下方法，框架将图片对应的下载操作放到UIView的一个自定义字典属性(operationDictionary)中，取消下载操作第一步也是从这个UIView的自定义字典属性(operationDictionary)中取出所有的下载操作，然后依次调用取消方法，最后将取消的操作从(operationDictionary)字典属性中移除。

![](http://upload-images.jianshu.io/upload_images/656644-40612c8707dadb6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 3.移除之前没用的图片下载操作之后就创建一个新的图片下载操作，然后设置到UIView的一个自定义字典属性(operationDictionary)中。


![](http://upload-images.jianshu.io/upload_images/656644-2f571ad0bca60941.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 4.看看如何创建一个新的图片下载操作,框架保存了一个失效的URL列表，如果URL失效了就会被加入这个列表，保证不会重复多次请求失效的URL。

![](http://upload-images.jianshu.io/upload_images/656644-6716a3ce4a660db1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


根据给定的URL生成一个唯一的Key,之后利用这个key到缓存中查找对应的图片缓存。

![](http://upload-images.jianshu.io/upload_images/656644-e48fccf6e177fe15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 5.读取图片缓存,根据key先从内存中读取图片缓存，若没有命中内存缓存则读取磁盘缓存，如果磁盘缓存命中，那么将磁盘缓存读到内存中成为内存缓存。如果都没有命中缓存的话，那么就在执行的doneBlock中开始下载图片。


![](http://upload-images.jianshu.io/upload_images/656644-68973ac3182fb40d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 6.图片下载操作完成后会将图片对应的数据通过completed Block进行回调

![](http://upload-images.jianshu.io/upload_images/656644-47cc23ae80d5d2fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在图片下载方法中，调用了一个方法用于添加创建和下载过程中的各类Block回调。

![](http://upload-images.jianshu.io/upload_images/656644-e191252d338aafb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加该URL加载过程的状态回调Block

![](http://upload-images.jianshu.io/upload_images/656644-7920b864207377fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果该URL是第一次加载的话，那么就会执行createCallback这个回调Block,然后在createCallback里面开始构建网络请求，在下载过程中执行各类进度Block回调。

![](http://upload-images.jianshu.io/upload_images/656644-0f04efcf3ac46f8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 7.当图片下载完成之后会回到done的Block回调中做图片转换处理和缓存操作

![](http://upload-images.jianshu.io/upload_images/656644-18294fe3db2ed79a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


回到UIImageView控件的设置图片方法Block回调中，给对应的UIImageView设置图片，操作流程到此完成。

![](http://upload-images.jianshu.io/upload_images/656644-c570c9791bb857d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


SDWebImage在加载图片网络请求的NSURLConnection的代理中对httpCode 做了判断，当httpCode为304的时候放弃下载，读取缓存。

![](http://upload-images.jianshu.io/upload_images/656644-6a8e47b73f5f0137.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###总结

SDWebImage作为一个优秀的图片加载框架，提供的使用方法和接口对开发者来说非常友好。其内部实现多是采用Block的方式来实现回调













