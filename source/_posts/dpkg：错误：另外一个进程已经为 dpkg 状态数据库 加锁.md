---
title: dpkg：错误：另外一个进程已经为 dpkg 状态数据库 加锁
index_img: /img/article/debug.jpg
categories: 
- Linux
tags:
- Linux
- Ubuntu
- debug
date: 2020-03-29 21:26:00
---

### ﻿**一、问题描述**

　　平时喜欢边听歌边敲代码（有种拯救世界的感觉），windows时一直用网易云，换了linux非常不方便，所以想给我的ubuntu（16.04）装一个。去官网找了一下，还真有linux版的，还特别标明是ubuntu 16.04（64位），良心软件啊，接下来就是载下来按部就班安装了。
　　载下来是.deb格式的，需要用以下命令：

```
dpkg -i 软件名.deb
```
　　开始一切都挺顺利，然而命运终于还是对我下手了。
　　![这里写图片描述](https://vistary.gitee.io/imgbed/images/dpkg_locked.jpg)

### **二、问题分析**

　　0. 注意，不是权限问题，我已经在root下了。
　　1. 说是“另外一个进程”，首先看了一下进程。
```
ps -a
ps -A
ps -aux
ps -aux | grep dpkg
```
　　试了各种命令，没有找到关于dpkg的进程。
　　（第一次向命运的抗争以失败告终）
　　2. 既然找不到心中的那个进程（我知道你一定在那，就是找不到你，救不出我的dpkg），重启吧，一切重新开始。
　　。。。。。
　　开机后竟然可以了，可能是那个进程已经kill掉了，放回了我的dpkg。
　　好了，继续安装网易云。。。
　　不过命运又皮了一下，有未能满足的依赖关系，解决办法看这篇文章，[
linux安装软件报错：有未能满足的依赖关系](https://blog.csdn.net/maizousidemao/article/details/82109038)

### **三、解决方法**

　　后来百度了一下，有两种方法可以解决这个问题：
　　方法一：重启系统，很方便。
　　方法二：删除dpkg下的lock文件，有人说这样做可能会让系统挂掉，试了一下，我的没问题（不保证你的也没问题啊，毕竟每个ubuntu脾气还是不一样的）。

```
rm  /var/lib/dpkg/lock
```

### **四、小结**

　　1. 进程占用问题，kill那个进程或重启系统。
　　2. 熟悉linux的文件系统。
