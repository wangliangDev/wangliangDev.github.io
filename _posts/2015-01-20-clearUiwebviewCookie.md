---
title: "IOS 清除UIWebview的缓存以及cookie"
layout: post
date: 2015-01-20
image: /assets/images/markdown.jpg
headerImage: true
tag:
- IOS技术

blog: true
author: 汪亮
description: 
---


#### cookie清除  
```java

NSHTTPCookie *cookie;
NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
for (cookie in [storage cookies])
{
    [storage deleteCookie:cookie];
}


```

#### 缓存清除

```java
  [[NSURLCache sharedURLCache] removeAllCachedResponses];
```