---
title: C++ Qt 实现鼠标拖动旋转功能
index_img: /img/article/Qt.jpg
categories: 
- 项目
tags:
- 项目
- Qt
- C++
- 面向对象
date: 2020-04-05 09:50:00
---

### 零、开始的开始

这是律盘，看古琴课程时，老师有一个，可以查找各弦散按音位，觉得挺好用，便做了一个。这里只聊聊怎么实现鼠标拖动旋转，可以借鉴到其他开发。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL3Zpc3RhcnkuZ2l0ZWUuaW8vaW1nYmVkL2dpZmhvbWVfNDk2eDUxNS5naWY#pic_center)

### 一、实现思路
#### 1. 旋转角度
一般旋转对象函数的输入都是角度，那么怎么获取这个角度呢？
鼠标拖动，当然是从鼠标的操作中获取。这个动作中，鼠标有三个状态：按下、拖动、释放，按下的点是旋转开始点（pressPoint），鼠标拖动旋转过程中的鼠标坐标点是当前点（currentPoint），释放时是旋转结束点，也是最后一个当前点。所以获取这 pressPoint 和 currentPoint，再加上旋转中心点（corePoint），就可以求得旋转角度。
代码实现如下：

```cpp
//鼠标拖动旋转的角度
int Disk::setAngle(){
    QLineF lineBegin(corePoint, pressPoint);
    QLineF lineEnd(corePoint, currentPoint);
    mouseAngle = 360 - lineBegin.angleTo(lineEnd);
    return mouseAngle;
}
```
为什么是用 360 减去，后面会讲。
#### 2. 旋转方向
对于准确的旋转方向，可以把旋转开始点和旋转结束点看作原点在旋转中心的两个向量，然后通过向量的外积确定旋转方向，具体见 [向量乘法与其几何意义](https://blog.csdn.net/maizousidemao/article/details/105270862) ，我们的目的不是获得精确的旋转方向，而且这样会增加软件后台的计算量，所以不选择这种方法。

使用角度定位法（我自己起的名字），就像时钟一样，是几点就在那个固定的位置，是几度，圆盘也在那个固定的位置。把角度分为过去的（oldAngle）和现在的（currentAngle），还有鼠标拖动旋转的角度（mouseAngle），他们都初始化为 0。

#### 3. 实现旋转
通过角度（currentAngle）获得旋转矩阵，通过旋转矩阵获得旋转后的图片，然后更新图片的显示。代码如下：
```cpp
QMatrix rotatematrix;
rotatematrix.rotate(currentAngle); //通过角度创建旋转矩阵
QPixmap fitpixmap = pix.transformed(rotatematrix,Qt::SmoothTransformation);//旋转

// 更新背景图
this->setIcon(fitpixmap);
this->setIconSize(QSize(fitpixmap.width(), fitpixmap.height()));
```

#### 4. 实现流程
对于鼠标的三个状态：按下、拖动、释放（释放后旋转就结束了，所以释放动作并不重要，这里不考虑），**鼠标按下时**，获取 pressPoint 坐标，同时将当前角度 currentAngle（也就是上一次旋转后的位置角度）赋值给旧的角度 oldAngle，代码如下：
```cpp
//鼠标按下事件
void Disk::mousePressEvent(QMouseEvent *event){
    pressPoint = event->pos();
    oldAngle = currentAngle;
}
```
**鼠标拖动时**，获取当前点坐标（currentPoint），然后利用 mouseAngle 和 oldAngle 计算 currentAngle。而 currentAngle = oldAngle + mouseAngle，然后再利用 currentAngle 对旋转对象进行定位。
到这里你可能会问，oldAngle + mouseAngle，一个方向是加，另一个方向怎么办？
所以我用了以下的算法：

```cpp
//鼠标移动事件
void Disk::mouseMoveEvent(QMouseEvent *event){
    currentPoint = event->pos();
	if(oldAngle > 360){
		oldAngle = oldAngle % 360;
	}
	
	currentAngle = oldAngle + setAngle(); //setAngle()返回mouseAngle 
	
	if(currentAngle > 360){
		currentAngle = currentAngle % 360;
	}
}
```
 oldAngle 和 currentAngle 都对 360 取余，保证他们小于等于 360，否则会出现跳变。当顺时针转动，currentAngle = oldAngle + mouseAngle 很容易理解，逆时针时本应该是 currentAngle = oldAngle - mouseAngle，这就涉及到 “1. 旋转角度” 中为什么要用 360 减去了。lineBegin.angleTo(lineEnd) 函数测量角度是从 lineBegin 到 lineEnd 沿逆时针方向测量的，示意图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200404222131583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
我选择了使顺时针理解容易，且 currentAngle 已初始化为 0 ，所以用 360 减去测量角度。如果鼠标逆时针拖动，比如图中右边的情况，mouseAngle = 307，oldAngle = 77，则 currentAngle = oldAngle + mouseAngle = 384，然后对 360 取余，正好是 24，最后利用这个角度进行旋转，一次操作结束。

### 二、完整代码

[ https://github.com/Holsey/rotateDisk ]( https://github.com/Holsey/rotateDisk )


现在该软件为 2.0 版本，添加了十二律、五音、简谱、西音、工尺对应查找功能，如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200404231025262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
如果你想使用这个软件，请联系我，QQ：1055311345。
