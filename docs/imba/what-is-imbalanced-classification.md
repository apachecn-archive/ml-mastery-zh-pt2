# 不平衡分类的温和介绍

> 原文:[https://machinelearning master . com/什么是不平衡分类/](https://machinelearningmastery.com/what-is-imbalanced-classification/)

最后更新于 2020 年 1 月 14 日

分类预测建模包括预测给定观察的类别标签。

不平衡分类问题是分类问题的一个例子，其中已知类中的例子分布是有偏差的。这种分布可以从轻微的偏差到严重的不平衡不等，少数群体中有一个例子，多数群体中有成百上千甚至上百万个例子。

不平衡分类对预测建模提出了挑战，因为大多数用于分类的机器学习算法都是围绕每个类具有相同数量的示例的假设而设计的。这导致模型的预测性能很差，特别是对于少数群体。这是一个问题，因为通常情况下，少数类更重要，因此该问题对少数类的分类错误比多数类更敏感。

在本教程中，您将发现不平衡分类预测建模。

完成本教程后，您将知道:

*   不平衡分类是当训练数据集中的类分布不均匀时的分类问题。
*   班级分布的不平衡可能会有所不同，但严重的不平衡对建模来说更具挑战性，可能需要专门的技术。
*   许多真实世界的分类问题具有不平衡的类别分布，例如欺诈检测、垃圾邮件检测和流失预测。

**用我的新书[Python 不平衡分类](https://machinelearningmastery.com/imbalanced-classification-with-python/)启动你的项目**，包括*分步教程*和所有示例的 *Python 源代码*文件。

我们开始吧。

![A Gentle Introduction to Imbalanced Classification](img/ae1f8b35c11e89f0c0e935eccee9ee93.png)

不平衡分类的温和介绍[约翰·梅森](https://flickr.com/photos/91451979@N00/14480378516/)摄，版权所有。

## 教程概述

本教程分为五个部分；它们是:

1.  分类预测建模
2.  不平衡分类问题
3.  阶层失衡的原因
4.  不平衡分类的挑战
5.  不平衡分类的例子

## 分类预测建模

分类是一个预测性建模问题，包括给每个观察分配一个类别标签。

> ……分类模型生成一个预测类，它以离散类别的形式出现。对于大多数实际应用，需要离散的类别预测来做出决定。

—第 248 页，[应用预测建模](https://amzn.to/32M80ta)，2013 年。

每个示例都由观察值和类标签组成。

*   **示例**:来自域(*输入*)的观察和关联的类标签(*输出*)。

例如，我们可以收集一朵花的测量值，并根据测量值对花的种类进行分类(*标记为*)。预测建模问题的类的数量通常在问题被框定或描述时是固定的，并且通常类的数量不会改变。

我们可以选择预测类成员的概率，而不是简单的类标签。

这允许预测模型在一系列选项中共享预测的不确定性，并允许用户在问题的上下文中解释结果。

> 像回归模型一样，分类模型产生连续值预测，其通常以概率的形式出现(即，任何单个样本的类别成员的预测值在 0 和 1 之间，总和为 1)。

—第 248 页，[应用预测建模](https://amzn.to/32M80ta)，2013 年。

例如，给定一朵花的测量值(*观测值*)，我们可以预测该花作为 20 种不同花中每一种的例子的可能性(*概率*)。

预测建模问题的类的数量通常在问题被框定或描述时是固定的，并且通常类的数量不会改变。

分类预测建模问题可能有两个类别标签。这是最简单的分类问题，被称为二分类或二分类。或者，问题可能有两个以上的类，例如三个、十个甚至数百个类。这些类型的问题被称为多类分类问题。

*   **二元分类问题**:分类预测建模问题，其中所有的例子都属于两类中的一类。
*   **多类分类问题**:一个分类预测建模问题，其中所有的例子都属于三类中的一类。

当处理分类预测建模问题时，我们必须收集训练数据集。

训练数据集是来自域的多个例子，包括输入数据(例如测量)和输出数据(例如类别标签)。

*   **训练数据集**:从问题域中收集的大量示例，包括输入观察和输出类标签。

根据问题的复杂性和我们可能选择使用的模型类型，我们可能需要域中的几十个、几百个、几千个甚至几百万个示例来构成训练数据集。

训练数据集用于更好地理解输入数据，以帮助为建模做好最好的准备。它还用于评估一套不同的建模算法。它用于调整所选模型的超参数。最后，训练数据集用于在所有可用数据上训练最终模型，我们可以在未来使用该模型对问题领域的新示例进行预测。

现在我们已经熟悉了分类预测建模，让我们考虑一下训练数据集中类的不平衡。

## 不平衡分类问题

属于每个类的例子的数量可以被称为类分布。

不平衡分类是指分类预测建模问题，其中每个类别标签的训练数据集中的示例数量是不平衡的。

也就是说，阶级分布不相等或接近相等，而是有偏差或偏斜的。

*   **不平衡分类**:分类预测建模问题，其中样本在类之间的分布不相等。

例如，我们可以收集花卉的测量值，并且有一个花卉物种的 80 个例子和第二个花卉物种的 20 个例子，并且只有这些例子构成我们的训练数据集。这是不平衡分类问题的一个例子。

> 当一个或多个类在训练数据中的比例与其他类相比非常低时，就会出现不平衡。

—第 419 页，[应用预测建模](https://amzn.to/32M80ta)，2013 年。

我们把这些类型的问题称为“T0”不平衡分类，而不是“T2”不平衡分类。不平衡是指曾经是平衡的，现在不再平衡的阶级分布，而不平衡是指本来就不平衡的阶级分布。

还有其他不太通用的名称可以用来描述这些类型的分类问题，例如:

*   罕见事件预测。
*   极端事件预测。
*   严重的阶级不平衡。

问题的不平衡由特定训练数据集中的类分布来定义。

> …必须针对特定的数据集或分布定义类别不平衡。因为需要类别标签来确定类别不平衡的程度，所以类别不平衡通常是相对于训练分布来衡量的。

—第 16 页，[不平衡学习:基础、算法和应用](https://amzn.to/32K9K6d)，2013。

用比率来描述数据集中类的不平衡是很常见的。

例如，不平衡度为 1 到 100 (1:100)的不平衡二进制分类问题意味着一个类中的每一个例子，另一个类中就有 100 个例子。

描述数据集中类不平衡的另一种方法是将类分布总结为训练数据集的百分比。例如，一个不平衡的多类分类问题可能有 80%的例子在第一类中，18%在第二类中，2%在第三类中。

现在我们已经熟悉了不平衡分类问题的定义，让我们看看为什么类可能不平衡的一些可能原因。

## 阶层失衡的原因

不平衡分类预测建模问题中类分布的不平衡可能有许多原因。

对于这种不平衡，我们可能要考虑两大类原因；它们是数据采样和域的属性。

不同类别的示例之间的不平衡可能是由从问题域中收集或采样示例的方式造成的。这可能涉及数据收集过程中引入的偏差，以及数据收集过程中出现的错误。

*   有偏抽样。
*   测量误差。

例如，也许例子是从一个狭窄的地理区域或一段时间收集的，班级的分布可能有很大不同，甚至可能以不同的方式收集。

收集观察结果时可能会出错。一种类型的错误可能是在许多示例中应用了错误的类标签。或者，从其中收集示例的过程或系统可能已经被损坏或削弱，从而导致不平衡。

通常，在不平衡由采样偏差或测量误差引起的情况下，可以通过改进的采样方法和/或校正测量误差来校正不平衡。这是因为训练数据集不能公平地表示正在处理的问题领域。

不平衡可能是问题域的一个属性。

例如，一个类的自然出现或存在可能支配其他类。这可能是因为在一个类中生成观察值的过程在时间、成本、计算或其他资源上更昂贵。因此，简单地从域中收集更多的样本来改善类分布通常是不可行或难以处理的。相反，需要一个模型来了解类之间的区别。

现在我们已经熟悉了类不平衡的可能原因，让我们考虑一下为什么不平衡的分类问题具有挑战性。

## 不平衡分类的挑战

阶级分布的不平衡会因问题而异。

一个分类问题可能会有点歪斜，比如说是不是有轻微的不平衡。或者，对于给定的训练数据集，分类问题可能具有严重的不平衡，其中一个类中可能有数百或数千个示例，而另一个类中可能有数十个示例。

*   **轻微不平衡**。一种不平衡的分类问题，其中训练数据集中的少量样本(例如 4:6)的分布是不均匀的。
*   **严重失衡**。一种不平衡的分类问题，其中示例的分布在训练数据集中是不均匀的(例如 1:100 或更多)。

> 当代作品中的阶级不平衡大多集中在 1:4 到 1:100 的不平衡比例上。[……]在实际应用中，如欺诈检测或化学信息学，我们可能会处理比例从 1:1000 到 1:5000 不等的不平衡问题。

——[从不平衡的数据中学习——开放的挑战和未来的方向](https://link.springer.com/article/10.1007/s13748-016-0094-0)，2016。

轻微的不平衡通常不是问题，这个问题通常可以像正常的分类预测建模问题一样处理。课程的严重不平衡可能对建模具有挑战性，并且可能需要使用专门的技术。

> 任何具有不等类分布的数据集在技术上都是不平衡的。然而，当每一类问题的例子数量明显不均衡时，或者在某些极端情况下，数据集就被认为是不平衡的。

—第 19 页，[从不平衡数据集](https://amzn.to/307Xlva)中学习，2018。

实例丰富的类称为主要类或多数类，而实例较少的类(通常只有一个)称为次要类或少数类。

*   **多数类**:有很多例子的不平衡分类预测建模问题中的类(或多个类)。
*   **少数类**:不平衡分类预测建模问题中的类，很少有例子。

当处理不平衡分类问题时，少数类通常是最感兴趣的。这意味着模型正确预测少数类的类标签或概率的技能比多数类更重要。

> 从不平衡数据中学习的发展主要是由许多现实生活中的应用程序推动的，在这些应用程序中，我们面临着数据表示不均匀的问题。在这种情况下，少数民族通常是更重要的一类，因此我们需要提高其识别率的方法。

——[从不平衡的数据中学习——开放的挑战和未来的方向](https://link.springer.com/article/10.1007/s13748-016-0094-0)，2016。

少数族裔更难预测，因为根据定义，这类人的例子很少。这意味着模型从这个类中学习例子的特征，以及将这个类的例子与多数类(或多个类)区分开，更具挑战性。

来自多数阶级(或多个阶级)的大量例子会淹没少数阶级。大多数用于分类预测模型的机器学习算法是在假设类分布相等的问题上设计和演示的。这意味着一个模型的天真应用可能只关注于学习大量观察结果的特征，而忽略了少数群体的例子，事实上，他们更感兴趣，他们的预测更有价值。

> ……大多数分类算法的学习过程往往偏向于多数类示例，因此少数类示例无法很好地建模到最终系统中。

—第七页，[从不平衡数据集](https://amzn.to/307Xlva)中学习，2018。

不平衡分类不是*解决的*

它通常仍然是一个未解决的问题，实际上必须针对每个训练数据集进行识别和解决。

即使面对更多的数据也是如此，所谓的“*大数据*”、“大型神经网络模型，所谓的“*深度学习*”以及非常令人印象深刻的竞争制胜模型，所谓的“*xboost*”

> 尽管在过去的 20 年里对不平衡学习进行了大量的研究，但是在现有的方法中仍然有许多缺点和问题需要解决。

——[从不平衡的数据中学习——开放的挑战和未来的方向](https://link.springer.com/article/10.1007/s13748-016-0094-0)，2016。

现在我们已经熟悉了不平衡分类的挑战，让我们看看一些常见的例子。

## 不平衡分类的例子

我们在实践中感兴趣解决的许多分类预测建模问题是不平衡的。

因此，不平衡分类没有得到比它更多的关注是令人惊讶的。

> 不平衡学习不仅给数据研究界带来了重大的新挑战，也在现实世界的数据密集型应用中提出了许多关键问题，从金融和生物医学数据分析等民用应用到监视和军事数据分析等安全和国防相关应用。

—第 2 页，[不平衡学习:基础、算法和应用](https://amzn.to/32K9K6d)，2013。

下面是十个问题域的例子列表，其中例子的类分布本质上是不平衡的。

很多分类问题可能会出现班级分布严重失衡；然而，观察固有不平衡的常见问题领域将使阶级不平衡的想法和挑战具体化。

*   欺诈检测。
*   索赔预测
*   默认预测。
*   流失预测。
*   垃圾邮件检测。
*   异常检测。
*   离群点检测。
*   入侵检测
*   转换预测。

示例列表揭示了不平衡分类预测建模的本质。

这些问题领域中的每一个都代表了一个完整的研究领域，其中来自每个领域的具体问题都可以作为不平衡分类预测建模来构建和探索。这突出了类不平衡分类的多学科性质，以及为什么机器学习从业者意识到问题并熟练解决它如此重要。

> 不平衡可能出现在任何数据集或应用程序中，因此，从业者应该意识到建模这类数据的含义。

—第 419 页，[应用预测建模](https://amzn.to/32M80ta)，2013 年。

请注意，大多数(如果不是全部的话)示例可能是二元分类问题。还要注意，少数民族的例子在某些方面是罕见的、极端的、不正常的或不寻常的。

还要注意的是，许多域被描述为“*检测*”，突出了在大量多数类的例子中发现少数类的愿望。

我们现在对不平衡分类预测建模有了一个稳健的概述。

## 进一步阅读

如果您想更深入地了解这个主题，本节将提供更多资源。

### 教程

*   [8 种对抗机器学习数据集中不平衡类的策略](https://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/)
*   [如何利用概率开发和评估朴素分类器策略](https://machinelearningmastery.com/how-to-develop-and-evaluate-naive-classifier-strategies-using-probability/)

### 书

*   第 16 章:严重阶级失衡的补救措施，[应用预测建模](https://amzn.to/32M80ta)，2013。
*   [不平衡学习:基础、算法和应用](https://amzn.to/32K9K6d)，2013。
*   [从不平衡数据集中学习](https://amzn.to/307Xlva)，2018。

### 报纸

*   [从不平衡的数据中学习——开放的挑战和未来的方向](https://link.springer.com/article/10.1007/s13748-016-0094-0)，2016。

### 文章

*   [异常检测，维基百科](https://en.wikipedia.org/wiki/Anomaly_detection)。

## 摘要

在本教程中，您发现了不平衡分类预测建模。

具体来说，您了解到:

*   不平衡分类是当训练数据集中的类分布不均匀时的分类问题。
*   班级分布的不平衡可能会有所不同，但严重的不平衡对建模来说更具挑战性，可能需要专门的技术。
*   许多真实世界的分类问题具有不平衡的类别分布，例如欺诈检测、垃圾邮件检测和流失预测。

你有什么问题吗？
在下面的评论中提问，我会尽力回答。