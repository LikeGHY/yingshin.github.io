---
layout: post
title:  "Git使用记录"
date:   2014-03-06 13:28:03
excerpt: "记录一些平时常用的Git命令"
categories: [tools]
tags: [Git]
---

使用Git已经有一段时间了，但是目前还是基本的使用，例如push,pull,commit。
这里会记录一些平时用到的Git命令，因此实用为唯一目的，只记录用法，不记录原因,不断添加。

```
#修改commit的内容
git commit --amend
#恢复未提交文件到上次提交状态
git rest --hard HEAD
#只恢复一个文件
git checkout -- filename
```
<!--more-->
