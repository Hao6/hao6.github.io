---
layout:     post
title:      初识NN(Nearest Neighbors)近邻算法
subtitle:   knn算法浅析
date:       2019-04-08
author:     hao6
header-img: img/post2_bg_knnicon.png
catalog: true
tags:
    - Machine Learning
---

> *世界上最遥远的距离，不是生与死，而是我就站在决策边界这边，你站在决策边界那边。*
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;-----*《世界上最遥远的距离》秦戈尔*
>															

最近在学习有关近邻算法的内容，在没学习之前，了解过knn等基于距离的算法，感觉这个算法比较简单，没有什么可供挖掘的东西，但在进一步了解之后，才发现自己的确是才疏学浅，近邻算法中需要注意的问题还有很多，例如距离（相似性）度量，k值选择，维度灾难等问题在工程运用中可能都会碰到。本篇**破文**希望能达到抛砖引玉的效果，总结一下最近的学习。

# 初识NN(Nearest Neighbours)算法

- 近邻算法，基于样本相似性大小来进行分类的一类分类算法
- 相似性度量
- k值的选择
- 时间复杂度
- 优点与不足，最近邻算法对离群点或者异常值敏感，使用k近邻能够降低这敏感影响；对于数据的分布也比较敏感，如果不同属性的分布非常不一致，则需要缩放处理（例如Normalize）。

## k-NN(k Nearest Neighbours)

- k-近邻算法，选择与待分类样本X相似度最大的k个已知样本来预测X的标签（类别），具体包括投票、加权投票等方式

### k-近邻算法的时间复杂度
**懒惰式**k-近邻，在训练时不做任何处理，在预测时计算可分为两个阶段：

    1. 计算待测样本X与训练集中每个样本的相似性，
    
    2. 对第一步计算得到的相似性进行升序排序，取前k个训练样本，对X的标签（类别）进行预测。 
    
懒惰式knn时间复杂度

设训练集包括M个D维向量，则训练时不消耗时间，而在预测的第一个阶段，平均时间复杂度是 $O{(N*D)}$ 
在训练的第二个阶段，平均时间复杂度是 $O{(\log(N) * N)}$ 

且在预测过程中，需要存储第一阶段计算产生的**N**个距离记录。

**非懒惰式**k-近邻算法，因为训练数据集往往在一定时间内是固定的，所以可以考虑采用某种数据结构来存储训练数据，然后期望能在预测时减小时间复杂度。一般使用KD-Tree或者Ball-Tree来存储训练数据。

### 相似性度量

相似性度量是近邻算法的基础，因为近邻算法必须依靠相似性决定选择哪些邻居来做决策，决策过程包括平均投票、加权投票等方式。而计算相似性的距离函数有很多种，必须依赖具体任务来选择距离函数。

- 欧式距离（**闵可夫斯基距离**的特殊情况）
- 余弦距离
- 曼哈顿距离（**闵可夫斯基距离**的特殊情况）
- 切比雪夫距离（**闵可夫斯基距离**的特殊情况）
- 标准化欧式距离
- 马氏距离
### k值的选择（trade-off about K）

- k值是一个超参数，调节k的过程实际上是k-近邻模型模型拟合程度的权衡，如果k**过分小**则模型表现为**过拟合**，反之如果k**过分大**，则模型表现为**欠拟合**。这一点在**决策边界**上表现的相当吻合，当k值**过分小**时，决策边界比较**曲折复杂**，反之，当k值增大时，决策边界会变得更加**平滑简单**。当然，这两种都不是理想情况，一般使用**交叉验证**等技术来选择合适的k值，k的一个经验区间是不超过 $ \sqrt{M} $ 。 

!['k=1'](https://picgo-image-hosting.oss-cn-beijing.aliyuncs.com/img/k1.png) 

!['k=2'](https://picgo-image-hosting.oss-cn-beijing.aliyuncs.com/img/k2.png) 

!['k=5'](https://picgo-image-hosting.oss-cn-beijing.aliyuncs.com/img/k3.png) 

!['k=10'](https://picgo-image-hosting.oss-cn-beijing.aliyuncs.com/img/k4.png) 

### 优点与不足

**优点**，懒惰版本的knn算法本身易于实现，而非懒惰式版本的knn算法实现的难度在于KD-Tree的构造与查找操作；knn算法的可解释性较强，理论比较成熟；相比于最近邻算法，对异常值不敏感；可以用于非线性分类、回归与分类等多种任务类型。

**不足**，样本不平衡的时候，对稀有类别的预测准确率低，一定程度上可以采用变种RediusNeighbours来修正，即根据待预测样本一定半径之内的样本预测带预测样本的标签，使得较稀疏邻域中的点使用较少的最近邻居进行分类；***维度灾难（curse of dimensionality）***，这是所有基于距离的算法在处理高维数据时都会面临的问题，在高维空间相似性度量将变得非常困难，换句话说，高维空间的相似性度量基本上失去了区分意义，因为大多数样本都距离非常远且分布均匀。下面是一种特殊情况下的证明。

假设 1：

随机变量X与Y独立且同服从于[0, 1]上的均匀分布，

$$
X \sim U_{(0, 1)} \\
Y \sim U_{(0, 1)} \\
X 与 Y 相互独立
$$

则随机变量X、Y的期望与方差分别为： 

$$
\begin{align*}
 \mathbb{E(X)} &= \mathbb{E(Y)} \\
 &= \int_{0}^{1}x\frac{1}{1-0}\mathrm{d}x \\
 &= \frac{1}{2} \\
 \mathbb{D(X)} &= \mathbb{D(Y)} \\
      &= \mathbb {E(X^{2})} - \mathbb {E(X)} ^ {2}\\
      &= \int_{0}^{1}x^2 \frac{1}{1-0}\mathrm{d}x - \frac{1}{4}\\
      &= \frac{1}{3} - \frac{1}{4}\\
      &= \frac{1}{12} 
\end{align*}
$$ 

令随机变量 

$$
Z = (X - Y) ^ 2
$$

则随机变量Z的期望与方差分别为： 

$$
\begin{align*}
E((X-Y)^2) &= E{(X^2 + Y^2 - 2*X*Y)}\\
           &= E{(X^2)} + E{(Y^2)} - E{(2*X*Y)}\\
           &= E{(X^2)} + E{(Y^2)} - 2*E{(X*Y)}\\
           &= D{(X)} + E(X)^2 + D{(Y)} + E(Y)^2 - 2*E{(X)}*E{(Y)}\\
           &= \frac{1}{12} + \frac{1}{4} + \frac{1}{12} + \frac{1}{4} - 2*\frac{1}{2}*\frac{1}{2}\\
           &= \frac{1}{6}\\
D((X-Y)^2) &= E((X-Y)^4) - {E((X-Y)^2)}^{2}\\
		   &= E(X^4+Y^4-4X^3Y+6X^2Y^2-4X^3Y) - (\frac{1}{6}) ^ {2}\\
		   &= E(X^4) + E(Y^4)-4E(X^3)E(Y)+6E(X^2)E(Y^2)-4E(X)E(Y^3) - \frac{1}{36}\\
		   &= \int_{0}^{1}x^4 \mathrm{d}x + \int_{0}^{1}y^4 \mathrm{d}y -4\int_{0}^{1}x^3 \mathrm{d}x \int_{0}^{1}y \mathrm{d}y + 6\int_{0}^{1}x^2 \mathrm{d}x \int_{0}^{1}y^2 \mathrm{d}y -4\int_{0}^{1}x \mathrm{d}x \int_{0}^{1}y^3 \mathrm{d}y - \frac{1}{36}\\
		   &= \frac{1}{5} + \frac{1}{5} -4*\frac{1}{4}*\frac{1}{2} + 6*\frac{1}{3}*\frac{1}{3}-4*\frac{1}{2}*\frac{1}{4} - \frac{1}{36}\\
		   &= \frac{7}{180}
\end{align*}
$$ 


假设2：

$X^D$ 与 $Y^D$ 都是**d**维空间中的向量，且每个坐标值都独立且同服从与[0, 1]上的均匀分布，即相当于在d维立方体上随机独立采了两个样本点。 

$$
X^D = (X_1, X_2,\dots,X_d)\\
Y^D = (Y_1, Y_2,\dots,Y_d)\\
X_i \sim U_{(0, 1)}, i>=1 \cap i<=d \\
Y^i \sim U_{(0, 1)}, i>=1 \cap i<=d \\
$$ 

令$Z(i) = (X(i) - Y(i))^2,i>=1 \cap i<=d$，则$R = \sum_{1}^{d}Z(i)$表示两个点欧氏距离的平方，则$R$的期望与方差、标准差分别为： 

$$
\begin{align*}
E(R) &= E(\sum_{1}^{d}Z_{i})\\
	 &= E(Z_{1}+Z_{2}+\dots+Z_{d})\\
	 &= E(Z_{1})+E(Z_{2})+\dots+E(Z_{d})\\
	 &= \frac{d}{6}\\
D(R) &= D(\sum_{1}^{d}Z_{i})\\
	 &= D(Z_{1}+Z_{2}+\dots+Z_{d})\\
	 &= D(Z_{1})+D(Z_{2})+\dots+D(Z_{d})\\
	 &= \frac{7d}{180} \\
\sigma &= \sqrt {D(R)}\\
       & \approx \frac{\sqrt{d}}{30}
\end{align*}
$$ 

对于 $X^D$ 与 $Y^D$ 来说， $\max (R) = \sqrt {(1^2+1^2+\dots)} = d$ ，即每一维度均在**d**维立方体的相对的顶点上，结合**假设2**中得到的 $E(R)$ 与 $\sigma$ ，可以看到**R**的期望值与$\max(R)$的数量级是一致的，而**R**的标准差则是更低的数量级，期望的含义是随机变量的平均取值，而标准差是随机变量偏离平均值的程度，在维度**d**非常大的情况下， $E(R)$ 相比于**R**的标准差$\sigma$来说非常大，即偏离平均值的程度相比于平均值本身来说非常小，所以可以验证***大多数样本都距离非常远且分布均匀***。

## k-近邻做回归任务

- 回归任务的流程与分类任务只有最后一步不同，回归任务需要求最近的k个邻居标签的平均值来作为预测值，同样的道理对于RediusNeighbours用来做回归任务。

## 参考与源代码

[knn算法的懒惰版本实现，决策边界绘制](https://github.com/Hao6/codingML/blob/master/src/knn.ipynb)

[sklearn官方文档](https://scikit-learn.org/stable/modules/neighbors.html)

[滑铁卢大学CSC411_2019](https://www.cs.toronto.edu/~mren/teach/csc411_19s/#overview)
