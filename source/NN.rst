Neural Network
================
大脑神经网络的结构
------------------
`The synapse <https://www.khanacademy.org/science/biology/human-biology/neuron-nervous-system/a/the-synapse>`_

.. _neuron:

基本概念
---------
1. layer

.. image:: img/nn-layer.png

2. 每个neuron的activate，weight matrix, bias的写法和意义

.. image:: img/nn-weight.png

Three Network Architectures
-----------------------------
from hilton week2

这三种网络结构和三种学习方式是否有对应关系？

网络结构设计
^^^^^^^^^^^^^

1. 一般默认，1 hidden layer, or >1 hidden layer, have same no. of hidden units in every layer(usually the more the better)

Backpropagation Algorithm
---------------------------
这一小节的内容摘自Andrew Ng Week5 p24b-24c, p34-36

神经网络求解weight matrix面临的问题
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
对于神经网络（hidden layer>=1）而言，求得整个网络的loss对每一层的每一个 θ :subscript:`ij` 的偏导数，就无法用与在机器学习中所用到的相同的方法求得了。BP算法就是为求这个偏导数而生的。

BP算法的思想
^^^^^^^^^^^^
1. forward propagation求得last layer的"error values"；
2. （这一步体现了BP的精髓）由last layer的"error values"进行backpropagation，求得L-1, L-2,...2层的"error values"；
3. 由layer j的"error values"求得layer j-1 to layer j的 **accumulated gradients**,Δ, j=2,...L
4. 对每一个training data计算1-3步
5. 由Δ求得梯度。

BP的推导
^^^^^^^^^^^^
推导过程参考了 `BP算法的理解与推导 <https://zhuanlan.zhihu.com/p/45190898>`_

.. image:: img/bp.jpg

weight matrix initialization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
详见 Andrew Ng week5 p30

1. 初值的取值范围
2. dimension of weight matrix from layer j to layer j+1
3. weight matrix的计算公式

BP算法的弱点
^^^^^^^^^^^^^^^
1. 梯度弥散（vanishing gradient problem）

当使用反向传播方法计算导数的时候，随着网络的深度的增加，反向传播的梯度（从输出层到网络的最初几层）的幅度值会急剧地减小。结果就造成了整体的损失函数相对于最初几层的权重的导数非常小。这样，当使用梯度下降法的时候，最初几层的权重变化非常缓慢，以至于它们不能够从样本中进行有效的学习。

例如，在AlexNet中，使用ReLU neuron替代了sigmoid neuron，成功解决了Sigmoid在网络较深时的梯度弥撒问题。

Construct a NN in TF
---------------------------



使用NN的一般流程
------------------
1. 准备数据

2. 设计网络结构(:ref:`Graph Level in TF <programming-model>`)

- 根据training data set, 设计网络的 **input layer** 和 **output layer**
- 根据应用场景设计 **hidden layer** , e.g. :ref:`How To Define CNN Graph <component_of_a_convoluntional_layer>`

3. 定义cost function

- cost function往往由整个CNN中最后一层的形态和意义来决定
- 最好加入 penalty factor——λ，以免过拟合

4. 定义使cost function最小化的优化算法

需要设置一个参数learning rate，用于余梯度下降时控制下降的速率。

5. 定义评估操作
6. Train Model

- 迭代地对数据进行训练

7. 在全部训练完成之后，在最终的测试集上进行全面的测试

应用场景
--------------
用神经网络可以解决

- 分类问题
- 回归问题

图像语义分割
^^^^^^^^^^^^^
图像的语义分割是像素级别的分类问题

《语义分割中的深度学习方法全解：从FCN、SegNet到各代DeepLab》
https://zhuanlan.zhihu.com/p/27794982

《十分钟看懂图像语义分割技术》
https://www.leiphone.com/news/201705/YbRHBVIjhqVBP0X5.html