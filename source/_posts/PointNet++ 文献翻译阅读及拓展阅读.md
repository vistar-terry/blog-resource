---
title: PointNet++ 文献翻译阅读及拓展阅读
index_img: /img/article/paper.jpg
categories: 
- PointNet++
tags:
- 点云
- 文献
- 深度学习
- 计算机视觉
date: 2020-8-1 21:02:36
math: true
---



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330183554388.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)

### 一、概览

[论文地址：https://arxiv.org/ftp/arxiv/papers/2003/2003.09644.pdf](https://arxiv.org/ftp/arxiv/papers/2003/2003.09644.pdf)
[代码下载：https://github.com/charlesq34/pointnet2](https://github.com/charlesq34/pointnet2)
**论文框架：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330183722694.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
**研究目的：** 增强 [PointNet](https://blog.csdn.net/maizousidemao/article/details/104855663) 识别细粒度模式的能力和对复杂场景的泛化能力，使其能够能够高效、稳健地学习深层点集特征。

**解决方法：** 
1. 递归应用PointNet的分层神经网络来对输入点集进行嵌套划分。
2. 在训练过程中借助随机输入丢失，学习对不同尺度上检测到的模式进行自适应加权，并根据输入数据对多尺度特征进行组合。

**研究贡献：** 
1. PointNet++在多个尺度上利用邻域来实现健壮性和细节捕捉，在学习关于距离度量的分层特征方面是有效的。
2. 针对非均匀点采样问题，提出了两个新的集合抽象层，根据局部点密度智能聚合多尺度信息。

**实验数据集：** MNIST、ModelNet40、SHREC15、ScanNet

### 二、相关工作
分层特征学习的思想已经非常成功。在所有的学习模型中，卷积神经网络是最突出的模型之一。然而，卷积不适用于具有距离度量的无序点集，这是我们工作的重点。

[Pointnet](https://arxiv.org/abs/1612.00593) 和 [Order matters](https://arxiv.org/abs/1511.06391) 研究了如何将深度学习应用于无序集合。但它们忽略了基础距离度量，即使只有一个点集。因此，它们无法捕获点的局部上下文，并且对全局集合转换和归一化敏感。在这项工作中，我们以从度量空间中采样的点为目标，并通过在设计中显式地考虑潜在的距离度量来解决这些问题。

从度量空间中采样的点通常带有噪声，并且采样密度不均匀。这影响了点特征的有效提取，给学习带来了困难。其中一个关键问题是为点特征设计选择合适的比例尺。以前，在几何处理领域或摄影测量和遥感领域，已经开发了几种方法来解决这一问题。与所有这些工作不同的是，我们的方法学会了以端到端的方式提取点特征和平衡多个特征尺度。

在3D度量空间中，除了点集，还有几种流行的深度学习表示法，包括体积网格和几何图。然而，在这些工作中，都没有明确地考虑到抽样密度不均匀的问题。

### 三、本文方法
#### 3.1 分层点集特征学习
虽然PointNet使用单个最大合并操作来聚合整个点集，但我们的新体系结构建立了点的分层分组，并沿着层次逐步抽象越来越大的局部区域。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330232039203.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
我们的层次结构由多个 set abstraction(这里不知道怎么翻译合适) 组成。在set abstraction，对一组点进行处理和抽象，以产生具有较少元素的新集合。set abstraction 由三个关键层组成：采样层、分组层和 PointNet 层。采样层从定义局部区域质心的输入点中选择一组点。然后，分组图层通过查找质心周围的“相邻”点来构建局部区域集。PointNet层使用迷你PointNet将局部区域模式编码为特征向量。
一个 set abstraction 以一个 $N×(d+C)$ 矩阵作为输入，该矩阵来自具有 $d$ 维坐标和 $C$ 维特征的 $N$ 个点。它输出一个$N^\prime×(d+C^\prime)$ 矩阵，这个矩阵具有 $N^\prime$ 个 $d$ 维坐标的二次采样点和总结局部上下文的新 $C^\prime$ 维的特征向量 。

**采样层**
给定输入点 $\{x_1，x_2，...，x_n\}$，我们使用迭代最远点采样(FPS)来选择点 $\{x_{i1}，x_{i2}，...，x_{im}\}$ 的子集，使得 $x_{ij}$ 是相对于其余点距离集合 $\{x_{i1}，x_{i2}，...，x_{i_{j−1}}\}$ 最远的点。与随机采样相比，在质心数目相同的情况下，该算法对整个点集的覆盖率更高。与扫描数据分布不可知的向量空间的CNN不同，我们的采样策略通过依赖于数据的方式生成 [感受野(receptive fields)](https://www.jianshu.com/p/2b968e7a1715)。

**分组层**
该层的输入是大小为 $N×(d+C)$ 的点集和一组大小为 $N^\prime×d$ 的质心的坐标。输出是大小为 $N^\prime×K×(d+C)$ 的点集的组，其中每组对应于一个局部区域，$K$ 是质心点邻域中的点数。这里，$K$ 因组而异，但是随后的 PointNet 层能够将不同数量的点转换成固定长度的局部区域特征向量。
在卷积神经网络中，像素的局部区域由在特定曼哈顿距离内使用数组索引的像素 [曼哈顿距离](https://baike.baidu.com/item/%E6%9B%BC%E5%93%88%E9%A1%BF%E8%B7%9D%E7%A6%BB/743092?fr=aladdin) 组成，这个特定的曼哈顿距离就是核大小。在从度量空间采样的点集中，点的邻域由度量距离来定义。

**PointNet 层**
在这一层中，输入的是数据大小为 $N^\prime×K×(d+C)$ 的点的 $N^\prime$ 个局部区域。输出中的每个局部区域从它的质心和编码质心邻域的局部特征中提取。输出数据大小为 $N^\prime×(d+C^\prime)$。
首先将局部区域中的点坐标转换成相对于质心点的局部框架：$x^{(j)}_i=x^{(j)}_i−\hat x^{(j)}$，其中 $i=1，2，...，K$，$j=1，2，...，d$，其中 $\hat x$ 是质心的坐标。我们使用 PointNet 作为局部模式学习的基本构建模块，通过使用相对坐标和点特征，我们可以捕获局部区域内的点对点关系。

#### 3.2 非均匀采样密度下的鲁棒特征学习
如前所述，点集在不同区域的密度不均匀是很常见的。这种不均匀性给点集特征学习带来了巨大的挑战。在密集数据中学习的特征可能不会推广到稀疏采样区域。因此，为稀疏点云训练的模型可能无法识别细粒度的局部结构。
理想情况下，我们希望尽可能仔细地检查某个点集，以捕捉密集采样区域中的最精细细节。然而，这种近距离检查在低密度区域是被禁止的，因为局部特征可能会因采样不足而被破坏。在这种情况下，我们应该在更大的范围内寻找更大规模的特征。为了实现这一目标，我们提出了密度自适应PointNet层(如下图)，当输入采样密度改变时，该层学习合并来自不同尺度区域的特征。我们称我们的具有密度自适应 PointNet 层的分层网络为PointNet++。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331213508767.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
在PointNet++中，每个 set abstraction 提取多个尺度的局部模式，并根据局部点密度进行智能组合。通过对局部区域进行分组，结合不同尺度的特征，我们提出了两种密度自适应层，如下所示。

##### 3.2.1 多尺度分组(MSG)
如上图(a)所示，捕捉多尺度模式的一个简单而有效的方法是应用不同尺度的分组图层，然后根据相应的 PointNet 来提取每个尺度的特征。连接不同尺度的特征以形成多多尺度特征。
我们对网络进行训练，学习一种优化策略，将多尺度特征结合起来。这是通过为每个实例分配的随机概率随机丢弃输入点来实现的，我们称之为随机输入丢弃（random input dropout）。具体地说，对于每个训练点集合，我们从 $[0，p]$ 中选择一个均匀抽样的丢弃率 $θ$，其中 $p≤1$。对于每个点，我们以概率 $θ$ 随机丢弃。在实践中，我们设置 $p=0.95$ 以避免生成空点集。在这样的过程中，我们给网络提供了各种稀疏性(由 $θ$ 引起)和不同均匀性(由丢弃的随机性引起)的训练集。测试期间，我们保留所有可用的分数。

##### 3.2.2 多分辨率分组(MRG)
上面的MSG方法非常耗费计算资源，因为它为每个质心点在大规模的邻域中运行局部PointNet。具体地说，由于质心点的数量通常相当大，因此时间成本很大。在这里，我们提出了另一种方法，它避免了这种昂贵的计算，但仍然保留了根据点的分布属性自适应聚合信息的能力。在上图(b)中，某一级别的区域 $L_i$ 的特征是两个矢量的串联。一个矢量(图中左侧)是通过使用 set abstraction 从较低级别 $L_{i−1}$ 汇总每个子区域的特征来获得的。另一个矢量(右)是通过使用单个 PointNet 直接处理局部区域中的所有原始点而获得的特征。
当局部区域的密度较低时，第一向量可能比第二向量更不可靠，因为在计算第一向量时的子区域包含更稀疏的点，并且出现更多的采样不足。在这种情况下，第二个向量的权重应该更高。另一方面，当局部区域的密度较高时，由于第一向量具有在较低级别递归地以较高分辨率进行检查的能力，因此第一向量有更精细细节的信息。
与MSG方法相比，该方法避免了在最低层进行大规模邻域特征提取，计算效率更高。

#### 3.3 用于集合分割的点特征传播算法
在集合抽象层中，对原始点集进行二次采样。然而，在集合分割任务中，如语义点标注，我们希望获得所有原始点的点特征。一种解决方案是始终将所有点采样为所有设置的抽象级别中的质心，但这会导致较高的计算成本。另一种方法是将特征从次采样点传播到原始点。
我们采用基于距离的插值和跨级跳过链接的分层传播策略。在特征传播层中，我们将点特征从 $N_l×(d+C)$ 点传播到 $N_{l−1}$ 点，其中 $N_{l−1}$ 和 $N_{l}(N_{l}≤N_{l−1})$ 是 set abstraction level $l$ 输入和输出的点集大小。我们通过在 $N_{l−1}$ 点的坐标上插值$N_{l}$ 点的特征值 $f$ 来实现特征传递。在众多插值选择中，我们使用基于 $k$ 近邻的反距离加权平均（下面公式中默认 $p = 2, k = 3$）。然后，将 $N_{l−1}$ 点上的插值特征与 set abstraction 中的跳过链接点特征连接起来。再将拼接的特征通过一个“单元 PointNet”，这类似于 CNNs 中的逐个卷积。应用几个共享全链接和 ReLU 层来更新每个点的特征向量。重复该过程，直到我们将特征传递到原始点集。

$$
f^{(j)}(x)=\frac{\sum^k_{i=1}w_i(x)f^{(j)}_i}{\sum^k_{i=1}w_i(x)} ,其中，w_i(x)=\frac{1}{d(x,x_i)^p},j=1,...,C
$$

### 四、实验
**数据集**：MNIST、ModelNet40、SHREC15、ScanNet
#### 4.1 欧氏度量空间中的点集分类
我们对从 2D(MNIST) 和 3D(ModleNet40) 欧几里德空间采样的点云进行了分类，并对我们的网络进行了评估。
把 MNIST 图像转换为具有数字像素位置的二维点云，从 ModelNet40 形状的网格曲面采样三维点云。默认情况下，我们对 MNIST 使用 512 点，对 ModelNet40 使用 1024 点。在下图表2的最后一行(我们的 baseline )中，我们使用面法线作为附加点特性，其中我们还使用更多的点(N=5000)来进一步提高性能。所有点集都归一化为零平均值，并且在一个单位球内。我们使用具有三个全连接层的三级分层网络。
对于MNIST图像，我们首先将所有像素的亮度归一化到 $[0，1]$ 范围内，然后选择所有强度大于 $0.5$ 的像素作为有效的数字像素。然后，我们将图像中的数字像素转换为坐标在 $[−1，1]$ 内的二维点云，其中图像中心是原点。创建扩充点是为了将设置为固定基数的点添加到固定基数(在我们的示例中为512)。我们抖动初始点云(随机平移高斯分布 $N(0，0.01)$ 并修剪到 $0.03$ )以生成增强点。对于ModelNet40，我们基于面面积从CAD模型曲面中均匀采样 $N$ 个点。
对于所有实验，我们使用学习率为 $0.001$ 的 [ADAM](https://arxiv.org/abs/1412.6980) 优化器进行训练。对于数据增强，我们使用随机缩放对象、扰动对象位置和点样本位置的方法。我们还遵循[V olumetric and multi-view cnns for object
classification on 3d data](https://arxiv.org/abs/1604.03265)随机旋转对象的方法以进行ModelNet40数据增强。我们使用 TensorFlow 和 GTX 1080、Titan X 进行训练，所有层都在 CUDA 中实现，以运行 GPU。将我们的模型训练到收敛大约需要20个小时。

**实验结果**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331223032373.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
在表1和表2中，我们将我们的方法与以前技术水平的代表性集合进行了比较。请注意，表2中的 PointNet(Vanilla) 是 [PointNet](https://blog.csdn.net/maizousidemao/article/details/104855663) 中不使用转换网络的版本，这相当于我们的分层网络只有一个级别。
在MNIST中，我们看到 PointNet(Vanilla) 和 PointNet 与我们的方法相比错误率分别降低了 $60.8\%$ 和 $34.6\%$。在 ModelNet40 分类中，我们还看到，使用相同的输入数据大小(1024个点)和特征(仅坐标)，我们的分类比 PointNet 强得多。其次，我们观察到基于点集的方法甚至可以获得比成熟的图像CNN更好或相近的性能。在 MNIST 中，我们的方法(基于二维点集)在网络CNN中达到了接近网络的精度。在 ModelNet40 中，我们使用普通信息的方法明显优于以前的最先进的方法 MVCNN。

**对采样密度变化的鲁棒性分析**
直接从真实世界获取的传感器数据通常存在严重的不规则采样问题，如下图。我们的方法选择多个尺度的点邻域，并通过对它们进行适当的加权来学习平衡描述性和鲁棒性。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331225651586.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
我们在测试期间随机落点(见下图左侧)，以验证我们的网络对非均匀和稀疏数据的鲁棒性。在下图右侧，我们看到 MSG+DP (在训练期间具有随机输入丢弃的多尺度分组)和 MRG+DP 对采样密度变化非常稳健。从1024个测试点到256个测试点，MSG+DP性能下降不到 $1\%$ 。此外，与其他方法相比，它在几乎所有的采样密度上都取得了最好的性能。PointNet vanilla 在密度变化下相当健壮，这是因为它关注全局抽象而不是精细细节。然而，与我们的方法相比，丢失细节也会使其功能较弱。SSG(Ablated PointNet++，每级单尺度分组)不能推广到稀疏采样密度，而 SSG+DP 通过在训练时间内随机丢弃点来弥补这一问题。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331225805558.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)

#### 4.2 面向语义场景标注的点集分割
为了验证我们的方法适用于大规模的点云分析，我们还对语义场景标注任务进行了评估。

目标是预测室内扫描中点的语义对象标签。[ScanNet](https://arxiv.org/abs/1702.04405) 在体素化扫描上使用完全卷积神经网络作为我们的 baseline。它们纯粹依赖于扫描几何体而不是RGB信息，并以每个体素为基础报告精确度。为了进行公平的比较，我们在所有的实验中都去掉了RGB信息，并按照 ScanNet 将点云标签预测转换为体素标签。我们还与 PointNet 进行了比较。
我们的方法在很大程度上超过了所有的 baseline 方法。与在体素化扫描上学习的 ScanNet 相比，我们直接在点云上学习，避免了额外的量化误差，并进行数据相关采样，以实现更有效的学习。与文献 PointNet 相比，该方法引入了分层特征学习，捕获了不同尺度的几何特征。这对于了解多个级别的场景和标记各种大小的对象非常重要。

**实验结果**
下图蓝色条代表基于每个体素的精度。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331231406985.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
下图可见，PointNet 正确捕捉房间的总体布局，但找不到家具。相比之下，除了房间布局之外，我们的方法在分割对象方面要好得多。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331231349979.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
**对采样密度变化的鲁棒性分析**
为了测试我们的训练模型在非均匀采样密度的扫描上的性能，我们合成了 Scannet 场景的虚拟扫描，并在此数据上评估了我们的网络。我们在三种设置(SSG、MSG+DP、MRG+DP)中评估我们的框架，并与基线方法 PointNet 进行比较。
为了从 ScanNet 场景生成训练数据，我们从初始场景采样 $1.5m×1.5m×3m$ 的立方体，然后将立方体保留在 $≥2\%$ 的体素被占用并且 $≥70\%$ 的表面体素具有有效注释的位置。我们在运行中对这样的训练立方体进行采样，并沿着右上轴随机旋转它。将增强点添加到点集，以形成固定基数(在我们的示例中为8192)。在测试期间，我们类似地将测试场景分割成更小的立方体，首先获得立方体中每个点的标签预测，然后合并同一场景中所有立方体中的标签预测。如果一个点从不同的立方体获得不同的标签，我们只需进行多数投票就可以得到最终的点标签预测。
性能比较如**实验结果**中黄条所示。我们看到，由于采样密度从均匀的点云转移到虚拟扫描的场景，SSG的性能大大降低。另一方面，MRG网络对采样密度漂移的鲁棒性更强，因为它能够在采样稀疏时自动切换到描述较粗粒度的特征。即使训练数据(均匀的点随机丢失)和密度不均匀的扫描数据之间存在领域差距，但我们的MSG网络受到的影响很小，在各种方法中达到了最好的准确率。这些都证明了我们的密度自适应层设计的有效性。

#### 4.3 非欧氏度量空间中的点集分类
在这一实验中，我们展示了我们的方法在非欧几里德空间上的推广。解决同一个物体不同姿态的识别问题。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401121257288.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
在非刚性形状分类中，一个好的分类器应该能够将图中的 (a) 和 (c) 正确地分类为相同的类别，即使它们的姿势不同，这需要内在结构的信息。SHREC15 中的形状是嵌入在三维空间中的二维曲面。沿曲面的测地线距离自然会产生度量空间。（Geodesic distances along the surfaces
naturally induce a metric space. ）实验表明，在该度量空间中采用 PointNet++ 是捕捉底层点集内在结构的有效途径。

对于文 [SHREC15 Track](https://www.cs.cf.ac.uk/shaperetrieval/files/Lian_3DOR_2015.pdf) 中的每种形状，我们首先构造了由成对测地距离诱导的度量空间。（we firstly construct the metric space in-
duced by pairwise geodesic distances.）使用 [Interior distance using barycentric coordinates](https://gfx.cs.princeton.edu/pubs/Rustamov_2009_IDU/paper.pdf) 的方法来获得模拟测地距离的嵌入度量。接下来，我们提取该度量空间中的本征点特征，包括 [WKS](http://imagine.enpc.fr/~aubrym/projects/wks/texts/2011-wave-kernel-signature.pdf)、[HKS](http://www.lix.polytechnique.fr/~maks/papers/hks.pdf) 和 [多尺度高斯曲率](http://www.geometry.caltech.edu/pubs/DMSB_III.pdf)。我们使用这些特征作为输入，然后根据底层度量空间对点进行采样和分组。通过这种方式，我们的网络学会了捕捉不受形状特定姿态影响的多尺度内在结构。另一种设计选择包括使用 $XYZ$ 坐标作为点要素或使用欧几里得空间 $\Bbb R^3$ 作为底层度量空间。实验结果显示，这些都不是最佳选择。

实验细节：
我们在每个形状上随机抽样 1024 个点进行训练和测试。为了生成输入的固有特征，我们分别提取100维的 WKS、HKS 和多尺度高斯曲率，得到每个点的 300 维特征向量。然后进行主成分分析，将特征维数降至 64。我们在  [Interior distance using barycentric coordinates](https://gfx.cs.princeton.edu/pubs/Rustamov_2009_IDU/paper.pdf)  后面用一个 8 维的嵌入来模拟测地线距离，它被用来描述我们的非欧几里德度量空间，同时选择点的邻域。

**实验结果：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401183458986.jpg#pic_center)
[DeepGM](https://www.researchgate.net/publication/316912821_Deep_Learning_with_Geodesic_Moments_for_3D_Shape_Classification) 提取测地线矩作为形状特征，并使用堆叠式稀疏自动编码器来处理这些特征并预测形状类别。我们的方法使用了非欧几里德度量空间和固有特征，在所有设置下都取得了最好的性能，并且大大超过了 DeepGM。

#### 4.4 特征可视化
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401185519380.jpg?type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21haXpvdXNpZGVtYW8=,size_16,color_FFFFFF,t_70#pic_center)
我们将通过分层网络的第一层核学习到的内容可视化。在空间中创建了一个体素网格，并聚合了在网格单元中激活神经元最多的局部点集(使用了最高100个示例)。高投票率的网格单元被保留下来，并转换回三维点云，这代表了神经元识别的模式。由于模型是在主要由家具组成的 ModelNet40 上训练的，所以在可视化中我们可以看到平面、双平面、线、角等结构。

### 五、结论
在这项工作中，我们提出了 PointNet++，这是一个强大的神经网络体系结构，用于处理度量空间中采样的点集。PointNet++ 递归地作用于输入点集的嵌套划分，并且在学习关于距离度量的分层特征方面是有效的。针对非均匀点采样问题，我们提出了两个新的集合抽象层，根据局部点密度智能聚合多尺度信息。这些贡献使我们能够在具有挑战性的三维点云基准上获得最先进的性能。
如何通过在每个局部区域共享更多的计算来提高网络的推理速度，特别是MSG层和MRG层的推理速度，是今后值得思考的问题。同样有趣的是，在高维度量空间中，基于CNN的方法在计算上是不可行的，而我们的方法可以很好地扩展。




