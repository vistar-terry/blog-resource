---
title: windows下OpenCV的安装部署详细教程
index_img: /img/article/opencv.jpg
categories: 
- OpenCV
tags:
- OpenCV
- 图像处理
- 计算机视觉
date: 2020-03-28 21:20:00
---

### ﻿**零、简介**

　　OpenCV的全称是Open Source Computer Vision Library，是一个跨平台的计算机视觉库。OpenCV是由英特尔公司发起并参与开发，以BSD许可证授权发行，可以在商业和研究领域中免费使用。OpenCV可用于开发实时的图像处理、计算机视觉以及模式识别程序。该程序库也可以使用英特尔公司的IPP进行加速处理。
　　OpenCV用C++语言编写，它的主要接口也是C++语言，但是依然保留了大量的C语言接口。该库也有大量的Python、Java and MATLAB/OCTAVE（版本2.5）的接口。这些语言的API接口函数可以通过在线文档获得。如今也提供对于C#、Ch、Ruby、GO的支持。
　　简单理解OpenCV就是一个库，是一个SDK，一个开发包，解压后直接用就可以。
　　由于OpenCV网站及软件都更新了，博客也小小改了一下，
[windows 下OpenCV的安装部署详细教程](https://www.jianshu.com/p/71d7bc4e0291)

### **一、下载OpenCV**

　　到[OpenCV官网](https://opencv.org/)下载你需要的版本。
　　点击RELEASES（发布）

![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807092613500.jpg)

由于OpenCV支持好多平台，比如Windows, Android, Maemo, FreeBSD, OpenBSD, iOS, Linux和Mac OS，一般初学者都是用windows，所以在这里下载Win pack
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807092814390.jpg)

点击Win pack 后跳出下面界面，等待5s自动下载。
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807093013393.jpg)

下载后是这样的
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807100920492.jpg)
然后双击他，解压，就是大佬们说的安装，实质就是解压一下，解压完出来一个文件夹，其他什么也没发生。你把这个文件夹放在哪都行，不过你要记住他在哪。
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807101015789.jpg)
正在解压
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807101038807.jpg)
解压完打开文件夹是这样的
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807101236346.jpg)
其中build是OpenCV使用时要用到的一些库文件，而sources中则是OpenCV官方为我们提供的一些demo示例源码

### **二、配置环境变量**

　　把OpenCV文件夹放好地方后，依次选择计算机—>属性—>高级系统设置—>环境变量，找到Path变量，选中并点击编辑，然后新建把你的OpenCV执行文件的路径填进去，然后一路点确定，这样环境变量就配置完了。
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807101427956.jpg)
OpenCV执行文件的路径这样找：
找到你解压好的OpenCV文件夹，依次选择build—>x64—>vc15—>bin，
然后是这样的
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807102134895.jpg)
这个路径就是我的OpenCV执行文件的路径，你的应该和我的差不多吧。
这里注意，如果你下载的是OpenCV2.x版本，选择build后，还需要选择x86或x64，然后是vc12（为什么不是vc10或vc11，一般都是选最新的），其他步骤大同小异。

### **三、部署OpenCV**

　　前面说了，OpenCV是一个SDK，得使用工具开发它，比如Visual Studio（当然有些大佬只用记事本或神一样的Vim），接下来就是在Visual Studio中部署OpenCV了。

#### **0. 安装Visual Studio**

　　因为主题是OpenCV，这个这里不讲了，请自行Google。

#### **1. 打开Visual Studio，新建工程**

　　初学者最好是建一个控制台工程，没有其他问题的干扰。

#### **2. 添加包含目录**

　　依次选择项目—>属性—>VC++目录—>包含目录—>编辑
　　找到你的包含目录添加就可以了，最好添加三个，我的是这样的：
　　D:\opencv\build\include
　　D:\opencv\build\include\opencv
　　D:\opencv\build\include\opencv2
　　![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807111405562.jpg)
　　

#### **3.添加库目录**

　　依次选择项目—>属性—>VC++目录—>库目录—>编辑
　　我的是D:\opencv\build\x64\vc15\lib
![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807111831210.jpg)

#### **4.添加附加依赖项**

　　依次选择项目—>属性—>链接器—>输入—>附加依赖项—>编辑
　　添加你的库文件名
　　![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807112228800.jpg)
　　库文件这样找：
　　![这里写图片描述](https://vistary.gitee.io/imgbed/images/opencv_install/20180807112415822.jpg)
　　有两个文件opencv_world341d.lib和opencv_world341.lib
　　如果配置为Debug，选择opencv_world341d.lib
　　如果为Release，选择opencv_world341.lib
　　这里注意，如果你下载的是OpenCV2.x版本，这里的库文件比较多，都填进去就可以了。

**到这里OpenCV的所有安装部署就结束了，可以进行下一步的使用和学习了。**
　　

