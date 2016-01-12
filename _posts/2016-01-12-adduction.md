---
layout: post
title: 什么是循环引用
category: 技术
comments: true
---


当两个不同的对象各有一个强引用指向对方，循环引用就产生了，多个对像之间，a持有B，B持有C，C持有A 也可产生循环引用，产生循环引用的后果就是内存泄漏，闪退