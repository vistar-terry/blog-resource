---
title: ubuntu16.04虚拟机桥接模式配置静态IP
index_img: /img/article/linux.jpg
categories: 
- Linux
tags:
- Linux
- Ubuntu
- 网络
date: 2020-03-29 20:20:00
---

本教程仅供学习，如急需解决问题，仅供参考。

在虚拟机安装好ubuntu16.04后，发现只有NAT模式可以上网，而桥接模式不能上网，经过一番摸索总结方法如下：

### 一、配置IP地址、默认网关、子网掩码

命令：

 **1. ifconfig**(查看网卡信息)
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTc1NjE2NjU0?x-oss-process=image/format,png)
有两块网卡，配置ens33（以太网）
我的是配置好的，你的显示可能和这个不一样，这一步只是看以太网卡的名字，配置时会用到

接下来切换用户，提高权限
**2. sudo -s** (进入管理员模式，修改配置文件需要较高权限)
**3. vi /etc/network/interfaces** (打开并编辑配置文件)
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTgwMTA1NDI1?x-oss-process=image/format,png)
打开文件后，编辑内容使如下图：
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTc1MDU0MjU0?x-oss-process=image/format,png)
配置说明：
**auto lo**
**iface lo inet loopback**

**auto ens33**（ens33为以太网卡，根据实际名称填写）
**iface ens33 inet static**
**address  &nbsp;&nbsp;&nbsp; 192.168.1.8**（IP地址，要和物理机在同一网段，且不要和局域网内其他设备IP冲突，查看方法见下）
**gateway &nbsp; 192.168.1.1**（默认网关，和物理机一样）
**netmask &nbsp; 255.255.255.0**（子网掩码，和物理机一样）

查看物理机IP等信息方法：
1. Windows + R 快捷键打开“运行”对话框
2.  输入CMD，点确定打开CMD命令行
3.  键入ipconfig，敲回车
4.  查找信息，有多块网卡，还有两块是虚拟机的网卡，不要看错
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTkwOTQ1MTc5?x-oss-process=image/format,png)

查看同局域网内其他设备IP方法：
1. Windows + R 快捷键打开“运行”对话框
2.  输入CMD，点确定打开CMD命令行
3.  键入arp &nbsp; -a，敲回车

编辑完配置文件，保存退出，如第2步没切换为管理员，这一步会禁止保存，不过也有解决办法，但太繁琐，有兴趣可以自行查阅。

### 二、配置永久DNS

命令：
 **vi /etc/resolvconf/resolv.conf.d/base**（这个文件默认是空的）
 ![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTkxNDA2OTAz?x-oss-process=image/format,png)
 输入上面所查询的物理机DNS服务器IP，如下图：
 ![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTkxMjQ4MjM3?x-oss-process=image/format,png)
保存退出

### 三、重启网络服务

命令：
**service network restart** 
这一步我的无法重启，也没找到有效的解决办法，如果你有好的解决办法，可以联系我，非常感谢。
![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTIwMTkxNTM0ODI1?x-oss-process=image/format,png)
这一步可以直接重启系统，命令：
**reboot**

### 四、检查

重启后，可以ping一下外网，看是否可以ping通
如ping不通，建议检查一下所修改配置文件

1. 看是否有个别字母错误，linux对大小写敏感
2. IP网段，DNS，子网掩码，默认网关是否和物理机一样
3. 如还不行，可以重装系统，再次配置，多次尝试练手可以加深印象
