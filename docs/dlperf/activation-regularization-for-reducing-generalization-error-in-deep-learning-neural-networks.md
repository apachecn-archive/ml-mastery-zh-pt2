# 深度学习中激活正则化的温和介绍

> 原文:[https://machinelearning master . com/activation-regulation-for-reduction-generation-error-in-deep-learning-neural-networks/](https://machinelearningmastery.com/activation-regularization-for-reducing-generalization-error-in-deep-learning-neural-networks/)

最后更新于 2019 年 8 月 6 日

深度学习模型能够从原始输入数据中自动学习丰富的内部表示。

这被称为特征或表征学习。更好的学习表示反过来可以导致对领域更好的洞察，例如通过学习特征的可视化，以及更好的利用学习特征的预测模型。

学习特征的一个问题是，它们可能对训练数据过于专门化，或者[过度训练，并且不能很好地推广到新的例子](https://machinelearningmastery.com/introduction-to-regularization-to-reduce-overfitting-and-improve-generalization-error/)。学习表示中的大值可能是表示过度的标志。活动或表示正则化提供了一种技术来鼓励学习的表示，即网络的隐藏层的输出或激活，保持小而稀疏。

在这篇文章中，你将发现激活正则化是一种提高神经网络中学习特征泛化能力的技术。

看完这篇文章，你会知道:

*   神经网络从数据和模型中学习特征，例如自动编码器和编码器-解码器模型，明确寻求有效的学习表示。
*   类似于权重，学习特征中的较大值，例如较大的激活，可以指示过度拟合模型。
*   在损失函数中增加惩罚，惩罚模型与激活的幅度成比例，可以产生更健壮和更一般化的学习特征。

**用我的新书[更好的深度学习](https://machinelearningmastery.com/better-deep-learning/)启动你的项目**，包括*分步教程*和所有示例的 *Python 源代码*文件。

我们开始吧。

![Activation Regularization for Reducing Generalization Error in Deep Learning Neural Networks](img/91047b76dd323ad2a2858819453a5005.png)

减少深度学习神经网络泛化误差的激活正则化
图片由[尼古拉斯·阿·托内利](https://www.flickr.com/photos/nicholas_t/585336580/)提供，保留部分权利。

## 概观

本教程分为五个部分；它们是:

1.  已学习功能的问题
2.  鼓励小规模激活
3.  如何鼓励小规模激活
4.  激活正则化的例子
5.  使用激活正则化的提示

## 已学习功能的问题

深度学习模型能够执行特征学习。

也就是说，在网络的训练过程中，模型会自动从输入的模式中提取显著特征或“*学习特征*”这些特征可以在网络中使用，以便预测回归的量或预测分类的类值。

这些内部表示是有形的东西。网络中隐藏层的输出表示模型在网络中该点学习的特征。

有一个研究领域专注于特征的高效和有效的自动学习，通常通过在使用第二网络从学习的特征重构原始输入之前，让网络将输入减少到小的学习的特征来研究。这种类型的模型被称为自动编码器或编码器-解码器，它们的学习特征对于学习更多关于领域的知识(例如通过可视化)和预测模型是有用的。

学习的特征或“*编码的输入”*”必须足够大以捕捉输入的显著特征，但也足够集中以不过度拟合训练数据集中的特定示例。因此，在所学特征的表达和概括之间有一种张力。

> 更重要的是，当编码器-解码器结构中代码的维数大于输入时，有必要限制代码携带的信息量，以免编码器-解码器可能简单地以琐碎的方式学习身份函数并产生不感兴趣的特征。

——[不变特征层次的无监督学习及其在对象识别中的应用](https://ieeexplore.ieee.org/document/4270182/)，2007。

正如网络中的大权重可能表示模型不稳定和过度拟合一样，学习到的特征中的大输出值也可能表示相同的问题。

期望在学习的特征中具有小的值，例如来自编码器网络的小输出或激活。

## 鼓励小规模激活

网络的损失函数可以更新，以便根据模型激活的程度来惩罚模型。

这类似于“T0”权重正则化，其中损失函数被更新以与权重的大小成比例地惩罚模型。一个层的输出被称为其“*激活*”，同样，这种形式的惩罚或正则化被称为“*激活正则化*或“*活动正则化*

> ……对神经网络中单元的激活进行惩罚，鼓励它们的激活稀疏。

—第 254 页，[深度学习](https://amzn.to/2NJW3gE)，2016。

编码器的输出，或者一般来说，神经网络中隐藏层的输出，可以被认为是模型中该点问题的表示。因此，这种类型的惩罚也可以称为“*表示正则化*”

希望有小的激活或者甚至很少的激活，并且大部分激活的值为零，这也被称为希望[稀疏](https://machinelearningmastery.com/sparse-matrices-for-machine-learning/)。因此，这种类型的惩罚也被称为“*稀疏特征学习*”

> 限制过完备代码的信息内容的一种方法是使其稀疏。

——[不变特征层次的无监督学习及其在对象识别中的应用](https://ieeexplore.ieee.org/document/4270182/)，2007。

自动编码器模型中稀疏学习特征的鼓励被称为“*稀疏自动编码器*”

> 稀疏自动编码器只是一种自动编码器，其训练标准除了重构误差之外，还包括代码层的稀疏性损失

—第 505 页，[深度学习](https://amzn.to/2NJW3gE)，2016。

当使用大于所需的隐藏层(例如，过完备)来学习可能导致过拟合的特征时，最常寻求稀疏性。稀疏惩罚的引入解决了这个问题，并鼓励更好的推广。

稀疏的过完备学习特征已经被证明比其他类型的学习特征更有效，提供了对输入中的噪声甚至变换的更好的鲁棒性，例如，图像的学习特征可以对图像中对象的位置具有改进的不变性。

> 稀疏-过完备表示具有许多理论和实践上的优势，这在最近的一些研究中得到了证明。特别是，它们对噪声具有良好的鲁棒性，并且提供了位置和频率的联合空间的良好拼接。此外，它们对于分类器是有利的，因为在高维空间中分类更容易。

——[深度信念网络的稀疏特征学习](http://papers.nips.cc/paper/3363-sparse-feature-learning-for-deep-belief-networks)，2007。

人们普遍关注的是表示的稀疏性，而不是小的矢量幅度。对这些表示的研究比神经网络的使用更普遍，被称为“稀疏编码”

> 稀疏编码为寻找刺激的简洁表示提供了一类算法；仅给定未标记的输入数据，它学习捕获数据中更高级特征的基函数。

——[高效稀疏编码算法](https://dl.acm.org/citation.cfm?id=2976557)，2007。

## 如何鼓励小规模激活

可以对每一层应用激活惩罚，可能只在学习表示的焦点所在的一层应用，例如编码器模型的输出或自动编码器模型的中间(瓶颈)。

可以应用一个约束，该约束增加了与层的矢量输出的幅度成比例的惩罚。

激活值可能是正的，也可能是负的，所以我们不能简单地对这些值求和。

计算激活量的两种常用方法是:

*   绝对激活值之和，称为 [l1 向量范数](https://machinelearningmastery.com/vector-norms-machine-learning/)。
*   平方激活值之和，称为 [l2 向量范数](https://machinelearningmastery.com/vector-norms-machine-learning/)。

L1 范数鼓励稀疏性，例如允许一些激活变为零，而 l2 范数通常鼓励小的激活值。使用 L1 范数可能是激活正则化更常用的惩罚。

必须指定一个超参数，指示损失函数将加重或关注损失的数量或程度。常用值是 0 到 0.1 之间的对数标度，如 0.1、0.001、0.0001 等。

活动正则化可以与其他正则化技术结合使用，例如权重正则化。

## 激活正则化的例子

本节提供了一些激活正则化的例子，以便为如何在实践中使用该技术提供一些背景。

在深度学习的早期历史中，正则化或稀疏激活最初是作为一种支持更深层神经网络发展的方法来寻求的。因此，许多例子可以利用像受限玻尔兹曼机器(RBMs)这样的架构，这些架构已经被更现代的方法所取代。权重正则化的另一个重要应用是在具有半标记或未标记数据的自动编码器中，即所谓的稀疏自动编码器。

蒙特利尔大学的 Xavier Glorot 等人介绍了使用校正的线性激活函数来促进表示的稀疏性。他们使用了 L1 惩罚，并在一系列经典的计算机视觉分类任务(如 MNIST 和 CIFAR10)上评估深度监督的 MLPs。

> 此外，在预训练和微调期间，将系数为 0.001 的激活的 L1 惩罚添加到成本函数中，以增加所学习的表示中的稀疏度

——[深度稀疏整流神经网络](http://proceedings.mlr.press/v15/glorot11a.html)，2011。

来自 Salesforce Research 的 Stephen Merity 等人将 L2 激活正则化与 LSTMs 一起用于自然语言处理的输出和循环输出，并与辍学正则化结合使用。他们在一系列语言建模问题上测试了一套不同的激活正则化系数值。

> 虽然实现起来很简单，但是活动正则化和时间活动正则化与其他复杂得多的正则化技术相比具有竞争力，并且提供了同等或更好的结果。

——[重温语言 RNNs 的激活正则化](https://arxiv.org/abs/1708.01009)，2017。

## 使用激活正则化的提示

本节提供了一些神经网络使用激活正则化的技巧。

### 适用于所有网络类型

激活正则化是一种通用方法。

它可以用于大多数，也许是所有类型的神经网络模型，尤其是最常见的多层感知器、卷积神经网络和长短期记忆递归神经网络。

### 与自动编码器和编码器-解码器一起使用

活动正则化可能最适合于那些明确寻求高效学习表示的模型类型。

这些模型包括自动编码器(即稀疏自动编码器)和编码器-解码器模型，例如用于序列到序列预测问题的编码器-解码器 LSTMs。

### 用不同的标准做实验

最常见的激活正则化是 L1 范数，因为它鼓励稀疏性。

尝试其他类型的正则化，例如 L2 范数，或者同时使用 L1 和 L2 范数，例如弹性网线性回归算法。

### 使用线性整流器

[整流线性激活函数，也叫 relu](https://machinelearningmastery.com/rectified-linear-activation-function-for-deep-learning-neural-networks/) ，是一种现在广泛应用于深层神经网络隐层的激活函数。

与 tanh(双曲正切函数)和 sigmoid(逻辑函数)等经典激活函数不同，relu 函数允许精确的零值。这使得它在学习稀疏表示时成为一个很好的候选，例如使用 l1 向量范数激活正则化。

### 网格搜索参数

正则化超参数通常使用较小的值来控制每次激活对惩罚的贡献。

也许可以从测试对数标度的值开始，例如 0.1、0.001 和 0.0001。然后按照最有希望的数量级进行网格搜索。

### 标准化输入数据

一般来说，将输入变量重新缩放到相同的比例是一种很好的做法。

当输入变量具有不同的标度时，网络权重的标度将相应地变化。大权重可以使非线性传递函数饱和，并降低层输出的方差。这可能会在使用激活正则化时带来问题。

这个问题可以通过规范化或标准化输入变量来解决。

### 使用过完备表示法

将所选的层配置为学习的特征，例如编码器的输出或自动编码器中的瓶颈，以具有更多可能需要的节点。

这被称为过完备表示，它将鼓励网络对训练示例进行过完备化。这可以通过强激活正则化来应对，以鼓励也是稀疏的丰富的学习表示。

## 进一步阅读

如果您想更深入地了解这个主题，本节将提供更多资源。

### 书

*   7.10 稀疏表示，[深度学习](https://amzn.to/2NJW3gE)，2016。

### 报纸

*   [深度稀疏整流神经网络](http://proceedings.mlr.press/v15/glorot11a.html)，2011。
*   [深度信念网络的稀疏特征学习](http://papers.nips.cc/paper/3363-sparse-feature-learning-for-deep-belief-networks)，2007。
*   [不变特征层次的无监督学习及其在目标识别中的应用](https://ieeexplore.ieee.org/document/4270182/)，2007。
*   [高效稀疏编码算法](https://dl.acm.org/citation.cfm?id=2976557)，2007。
*   [测量深度网络中的不变性](https://papers.nips.cc/paper/3790-measuring-invariances-in-deep-networks)，2009 年。
*   [视觉区稀疏深度信念网模型 V2](https://papers.nips.cc/paper/3313-sparse-deep-belief-net-model-for-visual-area-v2) ，2007。
*   [重温语言 RNNs 的激活正则化](https://arxiv.org/abs/1708.01009)，2017。
*   [监督学习中的稀疏活动和稀疏连通](http://jmlr.org/papers/v14/thom13a.html)，2013。

### 文章

*   [稀疏编码，Scholarpedia](http://www.scholarpedia.org/article/Sparse_coding) 。
*   [稀疏自动编码器，CS294A 讲义](https://web.stanford.edu/class/cs294a/sparseAutoencoder.pdf)。

## 摘要

在这篇文章中，您发现激活正则化是一种提高所学特征的泛化能力的技术。

具体来说，您了解到:

*   神经网络从数据和模型中学习特征，例如自动编码器和编码器-解码器模型，明确寻求有效的学习表示。
*   类似于权重，学习特征中的较大值，例如较大的激活，可以指示过度拟合模型。
*   在损失函数中增加惩罚，惩罚模型与激活的幅度成比例，可以产生更健壮和更一般化的学习特征。

你有什么问题吗？
在下面的评论中提问，我会尽力回答。