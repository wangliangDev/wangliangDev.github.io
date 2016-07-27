---
layout: post
title: 获取UIWebview的 Useragent，以及附加自定义字段到 Useragent
mage: /assets/images/markdown.jpg
headerImage: false
tag:
- IOS技术
blog: true

author: ""
description: ""

---

#### 获取 UIWebview 的useragent

```java
UIWebView* webView = [[UIWebView alloc] initWithFrame:CGRectZero];  
NSString* secretAgent = [webView stringByEvaluatingJavaScriptFromString:@"navigator.userAgent"];

```
这样我们就已经拿到了 useragent。


#### 附加自定义字段到 Useragent中

```java

NSString *newUagent = [NSString stringWithFormat:@"%@ appname/3.5.2",secretAgent];  
NSDictionary *dictionary = [[NSDictionary alloc]initWithObjectsAndKeys:newUagent, @"UserAgent", nil nil];  
[[NSUserDefaults standardUserDefaults] registerDefaults:dictionary];  

```
这里我们附加的是appname，以及对应的版本号。