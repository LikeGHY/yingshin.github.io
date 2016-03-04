---
layout: post
title:  "实现一个简单的boost::any类"
date: 2015-11-13 21:39:38
excerpt: "实现一个简单的boost::any类"
categories: [c/cpp]
tags: [boost]
---


boost::any是boost中一个非常短小的类，位于any.hpp中。  
目的在于提供一个数据结构，可以存放任意数据类型。  
例如：

```
vector<boost::any> vec;
vec.push_back(1);
vec.push_back("string");
```


<!--more-->


因此可以看出any类型的两个特点:  
1. 是一种类型  
2. 存储任意类型  

要实现1，any不能为模板类，要实现2，any需要能够接受模板参数。  
如何解决这看似矛盾的两点呢？  
解决办法就是any维护一个虚基类placeholder，真正的数据存在placeholder的子类holder里。  
holder类是一个模板类。

基于上述思路，实现了一个简化版本的Any。  
__IHolder__：  
虚基类，提供一个虚函数clone，print为测试用函数。  
__Holder__:  
模板类，数据存储在\_t，实现clone函数，对外复制自身数据，注意\_t是public的。  
__Any__:  
提供一个模板构造函数开辟\_holder数据指针  
析构函数释放\_holder数据指针  
实现复制构造函数和赋值函数，因为本身存储的是指针，所以需要防止浅复制导致同一地址被释放两次。注意这里需要有IHolder::clone完成，因为无法知道具体的数据类型，不能使用new Holder<T>操作。  
__any\_cast__:   
Any的友元模板函数，将Any类型转化为需要的类型。注意boost里有`any_cast` `unsafe_any_cast`两种方式。  

具体代码如下：

<script src="https://gist.github.com/yingshin/e5a32a57f267de1bfd1a.js"></script>
