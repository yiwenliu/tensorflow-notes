泛化
==========
How To Judge
-------------------
有很多防止overfit的方法，但是首先要判断是否发生了overfit，并辨别成因，使用对应的解决方法。

显然，只有把train set和validation set的cost function曲线放在一起，才能判断是否发生了ovefit。

L1 Regularization
-------------------
.. image:: img/l1-reg.png

L2 Regularization
-------------------
.. image:: img/l2-reg.png

从《tf实战》5.3的例子看出，在一个神经网络中，

1. 并非要对每一层的weights进行penalization。书中的例子，只对全连接层进行了penalization,而对于前面的卷积层、后面的softmax都没有进行这个操作。
2. bias不参与regularization

对training set进行预处理
------------------------------

Dropout
-------------------
详见 :ref:`CNN-Dropout <dropout>`

.. _data-aug:

Data Augmentation
--------------------------

“数据增强”可以增多training set，也是防止overfit的常用方法。用CNN进行图像识别时，其意义见《tf实战》p93

`this article <http://blog.csdn.net/u012162613/article/details/44261657>`_ 中的“数据集扩增”部分讲的很好，还有相关论文，暂时没有时间看。

`the article <https://zhuanlan.zhihu.com/p/31761796>`_ 详述了对MTCNN中所使用的"data set"进行data augmentation的过程

LRN
-------------------
LRN,局部响应归一化，AlexNet首次引入。用在神经网络结构中的哪里呢？

作用：对局部神经元的活动创建竞争机制，使得其中响应比较大的值变得相对更大，并抑制其他反馈较小的neuron，增强了模型的泛化能力。

使用梯度下降优化算法
------------------------
