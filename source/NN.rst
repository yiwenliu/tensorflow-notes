Neural Network
================
.. _neuron:

Neuron Model
-------------
根据neuron的激励函数（activation function）不同，常见的neuron model如下。需要根据使用场景选择不同的激活函数或者神经元。

Why Use Activation Functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- 非线性： 当激活函数是线性的时候，一个两层的神经网络就可以逼近基本上所有的函数了。但是，如果激活函数是恒等激活函数的时候（即f(x)=x），就不满足这个性质了，而且如果MLP使用的是恒等激活函数，那么其实整个网络跟单层神经网络是等价的。
- 可微性： 当优化方法是基于梯度的时候，这个性质是必须的。
- 单调性： 当激活函数是单调的时候，单层网络能够保证是凸函数。
- f(x)≈x： 当激活函数满足这个性质的时候，如果参数的初始化是random的很小的值，那么神经网络的训练将会很高效；如果不满足这个性质，那么就需要很用心的去设置初始值。
- 输出值的范围： 当激活函数输出值是 有限 的时候，基于梯度的优化方法会更加 稳定，因为特征的表示受有限权值的影响更显著；当激活函数的输出是 无限 的时候，模型的训练会更加高效，不过在这种情况小，一般需要更小的learning rate.

Sigmoid Neuron
^^^^^^^^^^^^^^^^

ReLU
^^^^^^^^^^^^^^^^^^^^^^^^^
Rectified Linear Neuron

PReLU
^^^^^^
Parametric Rectified Linear Unit，增加了参数修正的ReLU，来自于论文 `Delving Deep into Rectifiers:Surpassing Human-Level Performance on ImageNet Classification <https://arxiv.org/pdf/1502.01852.pdf>`_

.. image:: img/prelu.png

- 在MTCNN的PNet中使用了这个激励函数。
- 注意图中通道的概念，不同的通道对应不同的a :subscript:`i`
- 如果 ai=0，那么 PReLU 退化为 ReLU；如果 ai 是一个很小的固定值（如ai=0.01），则 PReLU 退化为 Leaky ReLU（LReLU）。 有实验证明，与 ReLU 相比，LReLU 对最终的结果几乎没什么影响。
- ReLU只增加了极少量的参数，也就意味着网络的计算量以及过拟合的危险性都只增加了一点点。特别的，当不同 channels 使用相同的 ai 时，参数就更少了。
- 对于任何新增的参数都会涉及两个问题，一是初始化，二是在整个计算过程中是否会变化
- 整个论文，ai 被初始化为 0.25。
- BP 更新 ai 时，采用的是带动量的更新方式（类似于learning rate），如下图：

.. image:: img/prelu-2.png

上式的两个系数分别是动量和学习率。
需要特别注意的是：更新 ai 时不施加权重衰减（L2正则化），因为这会把 ai 很大程度上 push 到 0。事实上，即使不加正则化，试验中 ai 也很少有超过1的。

Linear Neuron
^^^^^^^^^^^^^^^

Cost Function
----------------
整个神经网络的cost function由最后一层的neuron model决定。例如，linear neuron和softmax neuron的cost function有很大的区别

公式
^^^^^^
对于linear neuron而言，典型的cost function公式如下（from Andrew Ng Week6）

.. image:: img/cost-func.png

.. _effor-surface:

Error Surface
^^^^^^^^^^^^^^^
在Hilton和Andrew的课中，多次提及这个概念。下图就是一个linear neuron的error surface的垂直截面图和水平截面图，水平轴是each weight，垂直轴是error。

.. image:: img/nn-1.png

从上图可得：

- 梯度下降法的作用就是不断调整参数，使得模型的误差由“碗沿”降到“碗底”，参数由椭圆外部移动到椭圆的中心附近。
- weights每一个分量的变化(**gradient descent**)的矢量和就是cost function收敛的方向

典型的cost function
^^^^^^^^^^^^^^^^^^^^^^
Cross Entropy(互熵)
+++++++++++++++++++++

Generalization
---------------
How To Judge
^^^^^^^^^^^^^^
有很多防止overfit的方法，但是首先要判断是否发生了overfit，并辨别成因，使用对应的解决方法。

显然，只有把train set和validation set的cost function曲线放在一起，才能判断是否发生了ovefit。

L1 Regularization
^^^^^^^^^^^^^^^^^^^
.. image:: img/l1-reg.png

L2 Regularization
^^^^^^^^^^^^^^^^^^^
.. image:: img/l2-reg.png

Dropout
^^^^^^^^^

Data Augmentation
^^^^^^^^^^^^^^^^^^

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