---
title: ubuntu16.04更新源详细操作步骤
index_img: /img/article/linux.jpg
categories: 
- Linux
tags:
- Linux
- Ubuntu
date: 2020-03-29 10:07:00
---

由于linux系统自带的镜像源都在国外，国内用户下载或更新软件会比较慢，有时是非常慢，所以国内某些机构，如大学，研究院所，就在国内建了linux的镜像源服务器供国内linux用户使用，而我们要使用这些源，就要更改自己linux系统的更新源配置文件，接下来详述更新源操作步骤。

### **1. 首先我们要找到国内的镜像源路径**

 我选择了清华的镜像源，链接如下：
[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)
 打开链接如下图：
 ![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIyMTI0ODAyMDc0?x-oss-process=image/format,png)
接下来按图中提示操作即可。
也可以自己搜索其他的镜像源。

### **2. 备份系统自带更新源配置文件**

打开终端切换到管理员（修改配置文件需要较高权限，如不切换也可以在每条命令前加sudo，不过个人感觉有点麻烦），进入 **/etc/apt** 目录，找到 **sources.list** 文件，进行备份，如下图：
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIyMTMwMDE4NDEz?x-oss-process=image/format,png)
也可以直接用命令：**cp&nbsp; /etc/apt/sources.list&nbsp; /etc/apt/sources.list.backup** ，则不用切换目录直接备份。

###  **3.修改配置文件sources.list内容**

输入命令：**gedit &nbsp; sources.list** 打开文件，把文件内容全部删除，再把更新源路径粘贴进来。
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIyMTMwODU5OTI1?x-oss-process=image/format,png)

粘贴后如下图：
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIyMTMxMTI5NDI3?x-oss-process=image/format,png)
保存，退出。

###  **4. 更新源**

输入命令：**apt &nbsp; update** （也可以用apt-get&nbsp;update，[apt与apt-get的区别](https://blog.csdn.net/maizousidemao/article/details/79859669)）

开始更新源
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIyMTMxNDM1MjMy?x-oss-process=image/format,png)

更新完成
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIyMTMxNTI1MjUz?x-oss-process=image/format,png)
我用了1分1秒，这一步根据网速不同，消耗时间也不同，有时特别慢，要耐心等待，直到出现“完成”。

