---
layout: post
title: 20200531论文分享-Automatic detection on intracranial aneurysm from digital subtraction angiography with cascade convolutional neural networks
categories: MachineLearning
description: 
keywords: 
---

## 论文题目 

Automatic detection on intracranial aneurysm from digital subtraction angiography with cascade convolutional neural networks

(利用级联卷积神经网络从数字减影血管造影中自动检测颅内动脉瘤)


## 论文来源

[BioMedical Engineering OnLine](https://biomedical-engineering-online.biomedcentral.com/)：
一个同行评议的期刊，专门发表在生物医学工程领域相关的研究。

## 题目解读

### intracranial aneurysm

intracranial aneurysm(颅内动脉瘤)：颅内动脉瘤是颅内动脉血管的隆起或膨大导致的血管畸形。

![颅内动脉瘤示例](https:/climberclimbing.github.io/images/06.png)

脑内动脉瘤占总人口的3％，某些人群的风险更高。如果动脉瘤破裂，则会导致脑部出血。
颅内动脉瘤的早期检测以及形状的准确测量和评估在临床常规中很重要。 
这样可以监控动脉瘤的生长和破裂风险，从而做出明智的治疗决策[1]。

### digital subtraction angiography

digital subtraction angiography(DSA,数字减影血管造影)：经动脉注射造影剂，用时间减影法使血管显影。
此法动脉细小分支显示较清楚，造影剂用量少，导管不必深插至特定部位的动脉；
数字信息可贮存。有利于介入放射学的开展，并逐渐代替静脉数字减影血管造影。但有一定的创伤性。

另外还有两种无创的造影技术，即CTA(CT血管造影)、MRA(核磁共振血管造影)。

检测颅内动脉瘤的精确性：DSA > MRA > CTA。所以MRA和CTA常用于颅内动脉瘤的早期检测，而DSA则是颅内动脉检测的“黄金标准”。

![DSA CTA MRA](https:/climberclimbing.github.io/images/07.png)

DSA影像特点：

+ DSA影像包含了造影剂随从注射到扩散的变化过程，即包含时序特征。

+ DSA影像可以有正位和侧位两个拍摄角度，即包含多视角特征。

![DSA影像特点](https:/climberclimbing.github.io/images/08.png)

### cascade convolutional neural networks

所有的动脉瘤均在大动脉的分支部位产生，其中超过90%的颅内动脉瘤邻近或位于威利斯环区域。
而威利斯环区域仅占DSA影像中的一小部分，其他区域的DSA影像容易对颅内动脉瘤检测算法造成干扰。

![级联卷积神经网络](https:/climberclimbing.github.io/images/10.png)

所以本文提出 两阶段级联的颅内动脉瘤检测框架。

![级联卷积神经网络](https:/climberclimbing.github.io/images/09.png)

第一阶段为通过卷积神经网络在DSA影像中定位威利斯环位置然后将威利斯环区域裁切为威利斯环区域图像。

第二阶段为威利斯环区域图像中检测颅内动脉瘤。

## 论文创新点

1. 基于两阶段级联卷积神经网络框架(Dual input)，相对于经典数字图像处理方法，提高了颅内动脉瘤的检测速度和准确率。

2. 针对检测阶段的假阳性问题，提出 Regional average grayscale suppression 算法(RAGS, Regional average grayscale suppression)。

## 两阶段级联卷积神经网络框架

![两阶段级联卷积神经网络框架](https:/climberclimbing.github.io/images/11.png)

### Region localization stage

Region localization stage(RLS)：(网络结构参数已通过训练得到)将多个病人的侧面时间序列图片和与之对应的侧面时间序列图片(DICOM转PNG得到)作为输入，
通过特征金字塔网络(FPN,Feature Pyramid Networks 本文采用的网络主干为ResNet50)[2,3,4,5] 提取不同层次的图像特征，
然后将不同层次的特征分别送入anchor boxes(x坐标,y坐标,宽,高 Faster r-cnn)中去定位威利斯环位置，并将其输出为288X288的图片，用于检测阶段。

![The architecture of region localization stage (RLS).](https:/climberclimbing.github.io/images/12.png)

#### 时间序列图片

![DSA影像中的颅内动脉瘤与血管重叠](https:/climberclimbing.github.io/images/16.png)

DSA影像的每个像素点所对应的垂直三维空间中很大概率会包含多条血管，多条血管交叉产生的血管重叠会对临床医生在DSA影像中检测颅内动脉瘤带来干扰。
临床医生会利用造影剂随血液流动经过颅内动脉瘤与血管重叠时的不同显影效果对二者进行判别与区分。
以此为出发点，本文认为可以利用DSA影像时间序列特征对颅内动脉瘤进行检测，进而从计算机视觉中的高维映射角度量化临床医生判别颅内动脉瘤与血管重叠的方法。  
(使得训练得到的神经网络具备一定的判别血管重叠的能力，因此误识别的血管重叠往往具有较低的置信度)

#### FPN(feature pyramid networks)

特征金字塔网络(FPN)[8]：特征金字塔能够感知不同层次的抽象特征，实现多尺度目标检测。 

![feature pyramid networks](https:/climberclimbing.github.io/images/21.png)

+ (a)图像金字塔，即将图像做成不同的scale，然后不同scale的图像生成对应的不同scale的特征。这种方法的缺点在于增加了时间成本。有些算法会在测试时候采用图像金字塔。

+ (b)像SPP net，Fast RCNN，Faster RCNN是采用这种方式，即仅采用网络最后一层的特征。

+ (c)像SSD（Single Shot Detector）采用这种多尺度特征融合的方式，没有上采样过程，即从网络不同层抽取不同尺度的特征做预测，这种方式不会增加额外的计算量。作者认为SSD算法中没有用到足够低层的特征（在SSD中，最低层的特征是VGG网络的conv4_3），而在作者看来足够低层的特征对于检测小物体是很有帮助的。

+ (d)FPN采用这种方式，顶层特征通过上采样和低层特征做融合，而且每层都是独立预测的。


### Aneurysm detection stage

Aneurysm detection stage(ADS)这一阶段主要用于区分颅内动脉瘤和血管重叠。其包括四个步骤：dual-input、feature extraction、output、RAGS。

![The architecture of aneurysm detection stage (ADS).](https:/climberclimbing.github.io/images/13.png)

#### dual-input

同时结合正位影像和侧位影像作为双重输入单元，本文将待测视角下的威利斯环区域图像的像素值作为输入张量的前3个通道，将其对应的另一视角下的威利斯环区域图像的像素值作为输入张量的后3个通道，合并为一个6通道的输入张量。

#### RAGS

![the overlap of vessels was still easily misrecognized as an aneurysm](https:/climberclimbing.github.io/images/14.png)

针对检测阶段的假阳性问题(血管的重叠仍然很容易被误认为是动脉瘤)，本文提出RAGS算法。


## RAGS

### 基本经验

+ 大尺寸颅内动脉瘤：容易识别、置信度高、平均灰度值比周围血管低(颜色深)。

+ 小尺寸颅内动脉瘤：不容易识别、平均灰度值比周围血管高(颜色浅)。

+ 误识别的血管重叠：置信度低、平均灰度值比周围血管低(颜色深)。

![DSA影像中不同尺寸颅内动脉瘤示例](https:/climberclimbing.github.io/images/15.png)

### 算法流程图

![The fowchart of Regional Average Grayscale Suppression (RAGS) algorithm.](https:/climberclimbing.github.io/images/17.png)

如果颅内动脉瘤的置信度低于阈值c，则应用RAGS算法。

+ 第一步：首先使用OTSU算法[6]对图片进行二值化处理[7]；

+ 第二步：将二值化图片与原始图片进行逻辑与得到血管区域；

+ 第三步：计算颅内动脉瘤目标区域(红色框之内)的平均灰度值和其周围区域(红色框和绿色框之间)的平均灰度值；

+ 第四步：比较颅内动脉瘤目标区域的平均灰度值与其周围区域的平均灰度值，如果颅内动脉瘤目标区域的平均灰度值更低，则认为该目标是血管重叠，将其进行抑制，否则保留其颅内动脉瘤类标。


### 算法伪代码

![RAGS算法伪代码](https:/climberclimbing.github.io/images/18.png)

![RAGS算法伪代码说明](https:/climberclimbing.github.io/images/19.png)

### 阈值c的选择

将临床诊断结果与统计结果相结合：选择40名临床志愿者，其中20名被医生诊断为颅内动脉瘤患者，20名是非患者。
然后选择不同阈值c用RAGS算法诊断颅内动脉瘤。得到统计结果。

![阈值c的选择](https:/climberclimbing.github.io/images/20.png)

可以看出当c选择0.99时RAGS算法诊断结果与医生诊断结果最为接近。据此确定c的值为0.99。

## 算法对比

![算法对比](https:/climberclimbing.github.io/images/22.png)


## 后续

+ 本文对颅内动脉瘤的检测主要依靠目标检测的思想，这在它所提出的级联框架的RLS和ADS均有体现(FPN)。
其在RLS时，就可能将血管重叠区域也检测出来，这就容易造成假阳性。
针对这一问题，作者通过RGAS算法对检测结果进行进一步验证。

+ 需要设置参数：阈值c。

+ DSA是一种侵入性检查方式，患者可能不愿意选择。应考虑MRA和CTA等广泛的检查方法。

+ 目标检测的目标物体的一个边界框(通常是矩形)，
而图像分割则是将每一像素与其表示的目标类别对应起来，即从像素水平上对目标定位，不再是简单地画矩形框。



## 参考

[1] [Aneurysm Detection And segMentation Challenge-Details](http://adam.isi.uu.nl/details/)

[2] [浅谈多尺度目标检测](https://www.cnblogs.com/wangguchangqing/p/12012508.html)

[2] [目标检测:目标检测中的Anchor详解]()

[3] [Feature Pyramid Networks for Object Detection 论文笔记](https://blog.csdn.net/jesse_mx/article/details/54588085)

[4] [特征金字塔网络FPN](https://zhuanlan.zhihu.com/p/39185919)

[5] [resnet，retinanet,fpn网络结构及pytorch实现代码](https://blog.csdn.net/weixin_38145317/article/details/103296449)

[6] [OTSU算法（大津法—最大类间方差法）原理及实现](https://blog.csdn.net/weixin_40647819/article/details/90179953)

[7] [图像二值化,阈值处理(十)](https://www.cnblogs.com/angle6-liu/p/10673585.html)

[8] [FPN（feature pyramid networks）算法讲解](https://blog.csdn.net/u014380165/article/details/72890275/)



