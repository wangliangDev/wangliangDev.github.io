---
title: "iOS9 UniversalLink使用"
layout: post

image: /assets/images/markdown.jpg
headerImage: false
tag:

blog: true

author: ""
description: ""

---

## 简介

通用链接是Apple在WWDC2015上为iOS9引入的一个新功能，是通过传统HTTP链接来启动App的技术。可以使用相同的网址打开网站和App。

通过唯一的网址，就可以链接到App中具体的视图，不需要特殊的schema。如果用户没有安装App则链接到对应的普通网页。

* 先决条件
l至少Xcode7

l至少iOS9beta2（之前的测试版本要做额外的工作）

l有一个注册通过SSL访问的域名

3操作步骤
3.1注册App并打开Associated Domains服务
如果你还没有注册App，则需要登陆developer.apple.com注册一个。

在Identifiers下AppIDs找到自己的App ID，编辑打开Associated Domains服务。

blob.png

选中，并保存。

3.2在Xcode中开启Associated Domains服务
a.打开Associated Domains服务

blob.png

如果出现如下图所示的情况请检查下自己所选的组是否正确，以及Xcode工程中的bundle identifier和注册的App Identifier是否一致。

blob.png

b.添加域名，点击Associated Domains的“+”添加前缀为applinks:的域名，如下图所示

blob.png

c.确认在entitlements文件包含在工程中

3.3配置apple-app-site-association文件
a.创建apple-app-site-association的json文件

文件格式如下图所示：

blob.png

其中apps项必须对应一个空的数组。details项对应一个字典的数组，网站所能支持的每个app一个字典。

appID对应项由前缀和ID两部分组成，可以在developer.apple.com中的Identifiers→AppIDs中点击对应的App ID查看。


paths对应域名中的path，用于过滤可以跳转到App的链接，支持通配符‘*’，‘？’以及‘NOT’进行匹配，匹配的优先级是从左至右依次降低。

b.服务器配置要求：

域名需要SSL证书，如果不支持HTTPS，则需要对apple-app-site-association进行SSL认证。认证命令如下：

cat apple-app-site-association-unsigned | openssl smime -sign -inkey yourdomain.com.key -signer yourdomain.com.cert -certfile digicertintermediate.cert -noattr -nodetach -outform DER > apple-app-site-association

服务器配置要求站点必须是youdomain.com/apple-app-site-association，请求头是‘application/pkcs7-mime’，返回HTTP码是200。

3.4响应处理
点击链接跳入App，由UIApplicationDelegate中的application:continueUserActivity:restorationHandler:函数进行处理，通过如下方式获取URL，然后对不同的URL进行不同的响应。



4一些注意的事项
1.apple-app-site-association不需要.json后缀。

2.如果要对没有path的域名进行支持（如：www.163.com），在json文件的paths中用通配符’*’是不行的，需要在paths数组中加入’/’进行匹配。

3.对json文件的请求仅在App第一次启动时进行，如果此时网络连接出了问题apple会缓存请求，等有网的时候再去请求，而实际测试抓包并没有请求故通用连接会失效。

4.paths匹配的优先级是从左至右依次降低，但需要明确，否则会出问题。比如"paths":[ "NOT /together/*","*"]，在IOS9.2上path为/together/*都不会跳到App，但是在IOS9.0上会跳到。

5.从iOS 9.2开始，在相同的domain内Universal Links是不work的，必须要跨域才生效