---
title: 斯坦福机器学习系列课程（一） 图像分类：数据驱动的研究方法
---
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=429482207&auto=0&height=66"></iframe>

### 斯坦福机器学习系列课程（一） 图像分类：数据驱动的研究方法

#### 图像分类
**概念** 
将图像作为输入，然后从固定的分类集合中选择一个标签为其贴上，一般而言，一些突出的计算机视觉任务，例如物体检测、图像分割都可以转化成为图像分类任务。

<!--more-->

**举个栗子** 
现在我有一幅图片，然后准备了4中分类标签：猫、狗、帽子和杯子。如下图所示，这张带有猫的图片尺寸是400x248，带有RGB三通道，每个通道位宽是8位，所以这幅图片的原生数据个数是：400x248x3=297600，每个数据范围是0-255，图像分类的任务便是把这么多的数据输入，然后最终输出“猫”这个分类。

![All text](http://7xopvd.com1.z0.glb.clouddn.com/classify.png)

**挑战**
虽然在这样一幅图中寻找一只猫这样的任务对人类而言很简单，但在技术那几视觉算法领域，其中包含的一些挑战和困难是值得去考虑的。下面罗列了一些存在的问题，需要记住的是，原生图像的表示是三维的，即RGB：
- **视角变化** 同样的物体，由于相机位置的不同，造成了物体在图像中的方位不同。
- **伸缩变化** 物体在图像中的尺寸可能和现实世界中分类好的物体尺寸不同
- **形变** 许多物体的形状并不是刚性的，可能会有很极端的形变方式
- **光照条件** 光照强度在像素级上的影响是很严重的，例如光照太强引起的过饱和
- **背景杂乱** 物体可能会被混入背景，使得物体识别变得困难
- **内部分类的广泛** 一些感兴趣的分类可能会很广泛，例如“椅子”这个分类，然而椅子的种类还有很多种，每一种的外观都不一样

![All text](http://7xopvd.com1.z0.glb.clouddn.com/challenges.jpeg)

一个优秀的好的图像分类模型必须克服这些多样性带来的挑战，与此同时还要保持对内部分类的敏感性。

#### 数据驱动的研究方法
到底该怎么写一个能对图像进行分类的算法呢？比起其他普遍的算法，比如为一个数组进行排序，图像分类算法并不是那么显而易见。所以，我们采取的方式并不是在代码里直接能体现分类这个动作，而是像给你的孩子一台电脑，里面存放了各种类别的图像样本，然后教你孩子观察和学习这些图像样本，并对他们进行分类。当孩子学习一段时间过后，给他一幅图像，如果学习效果显著，那么他之后就能对新来的图像进行分类了。

上面这样的方法也被称为**数据驱动的研究方法**，这样的方法很依赖被标注好分类的训练**数据集**积累，下图展示了数据集的大概面貌：
![Alt text](http://7xopvd.com1.z0.glb.clouddn.com/trainset.jpg)

**数据分类的流程**
从之前的介绍我们可以看出，图像分类所要做的事情便是将像素点输入，最后为其贴上一张分类标签，大致的流程可以用下面几项来描述：
- **输入： **输入包含了N张图像，每一张带有一个正确的分类的标签，总共有K个不同的分类，这样的数据便称为**训练集**
- **学习：**我们的任务是利用这样的训练集去学习每一个样本属于哪个分类，这个过程也被称为**训练分类器**或者**学习模型**
- **评估： **最后，我们通过输入一个分类器从没接收过的新的图片集，然后验证此时的分类结果来验证分类器的可靠性和准确性。验证的手段一般是将分类器的预测结果和图像本身的正确分类进行比较，直观上看，我们希望看到的是预测大多结果都与正确的客观结果相符。

#### 最近邻近分类器
下面以**最近邻近分类器**作为数据驱动研究方法的第一个例子。虽然它和卷积神经网络（CNN）并不相关，而且在实际应用中几乎不被使用，但是通过它我们可以获取一些关于图像分类问题的基本实现方法。

分类器采用的数据集：[CIFAR-10](http://www.cs.toronto.edu/~kriz/cifar.html)

CIFAR-10里面包含了60000张32x32图片，每一张图片都被标注了类别，例如飞机、汽车、鸟等共计10个分类。这6万张图片中，有50000张是训练集，10000张是测试集。在下面这幅示意图中，你可以看到来自于10个分类的，每个分类下10张随机图片：
![Alt text](http://7xopvd.com1.z0.glb.clouddn.com/nn.jpg)

最近邻近分类器是这样工作的，它从测试集选取了一张图片，并与训练集中的每一张去比较，然后预测和它最相近的训练图片的分类。从上面的右图可以看出，对于左侧的每一张测试图片，右边都给出的10张和它最相似的图片。但是需要注意的是，找出的这10张图片的分类并不一定和预期的相符，一般而言只有3张，例如第8行的马头，匹配得到的第一张红色的汽车则不是马这个分类。误匹配的原因可能是红车的图片背景也是黑色的。

具体而言，比较的过程是基于像素级的，对于测试图片和训练图片而言，每张图片的像素个数是：32x32x3，将两幅图像的像素数据进行相减，结果取绝对值，得到差值图像，然后将插值图像里的像素数据再相加，作为衡量两幅图像相似程度的依据，过程如下图所示：

![All text](http://7xopvd.com1.z0.glb.clouddn.com/nneg.jpeg)

如果将两幅图像的像素数据分别用两个一维向量$(I_1,I_2)$表示的话，它们间的差距可以用$L_1$距离来表示：$$L_1=|I_1-I_2|$$

下面是最近邻近分类器的代码实现：
**1. **载入CIFAR-10的数据，4个一维数组：训练图片、标签、测试图片、标签，$X_tr$代表的是来自训练样本的50000x32x32x3个像素点数据，$Y_tr$代表的是50000个训练样本的固有分类，范围是0-9：

```python
Xtr, Ytr, Xte, Yte = load_CIFAR10('data/cifar10/') # a magic function we provide
# flatten out all images to be one-dimensional
Xtr_rows = Xtr.reshape(Xtr.shape[0], 32 * 32 * 3) # Xtr_rows becomes 50000 x 3072
Xte_rows = Xte.reshape(Xte.shape[0], 32 * 32 * 3) # Xte_rows becomes 10000 x 3072
```

**2. **有了数据，开始训练和评估我们的分类器：

```python
nn = NearestNeighbor() # create a Nearest Neighbor classifier class
nn.train(Xtr_rows, Ytr) # train the classifier on the training images and labels
Yte_predict = nn.predict(Xte_rows) # predict labels on the test images
# and now print the classification accuracy, which is the average number
# of examples that are correctly predicted (i.e. label matches)
print 'accuracy: %f' % ( np.mean(Yte_predict == Yte) )
```

精度这个指标一般用来衡量评估结果的好坏，它表示的是正确预测结果的百分比。

$trrain(X,y)$函数两个参数：$X$是训练样本，$y$是样本的固有标签

$predict(X))$函数一个参数：$X$是测试样本，返回的是测试结果标签

分类器的用法介绍完了，下面是分类器实现的代码以及$L_1$距离的定义：

```python
import numpy as np

class NearestNeighbor(object):
  def __init__(self):
    pass

  def train(self, X, y):
    """ X is N x D where each row is an example. Y is 1-dimension of size N """
    # the nearest neighbor classifier simply remembers all the training data
    self.Xtr = X
    self.ytr = y

  def predict(self, X):
    """ X is N x D where each row is an example we wish to predict label for """
    num_test = X.shape[0]
    # lets make sure that the output type matches the input type
    Ypred = np.zeros(num_test, dtype = self.ytr.dtype)

    # loop over all test rows
    for i in xrange(num_test):
      # find the nearest training image to the i'th test image
      # using the L1 distance (sum of absolute value differences)
      distances = np.sum(np.abs(self.Xtr - X[i,:]), axis = 1)
      min_index = np.argmin(distances) # get the index with smallest distance
      Ypred[i] = self.ytr[min_index] # predict the label of the nearest example

    return Ypred
```

用这个分类器跑CIFAR-10的数据，大概能达到38.6%的准确度，而随机猜测大概只能有10/100-10%的准确度。对于神经网络而言，大概能到95%的准确度，能与人脑相媲美

对于距离计算选择，还有很多。

（Lucien：第一天的学习总是告一段落了，第一章还是介绍了一些基本概念，读起来还是很轻松的，晚上还在实验室干了点活，perfect day！）