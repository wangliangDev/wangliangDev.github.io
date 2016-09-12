---
layout: post
title: php安装找不到PHP-fpm
mage: /assets/images/markdown.jpg
headerImage: false
tag:
- PHP
blog: true

author: ""
description: ""

---


### php安装找不到PHP-fpm

这两天安装LNMP，在安装php的时候找不到PHP-fpm,网上各种各样的方法都试了，还是不行，RPM包，tar包安装都试过，rpm包怎么试都找不到PHP-fpm,就用tar包来安装，就拿php5.6.23来讲，我们一般下载了包解压出php5.6.23文件夹对吧，然后就是配置对吧，注意：`./configure --prefix=/usr/local/php...(后面还有一些其它参数) ` 一般都是这样对吧 问题就是出在这里，这个目录就是安装的目录`--prefix=/usr/local/php` 记得这里一定要改成刚刚解压出来的php5.6.23文件夹的目录，比如说你php5.6.23文件夹的目录是在`/usr/local`这下面，类似于`/usr/local/php5.6.23`
那么这个`./configure --prefix=/usr/local/php5.6.23`这样