Neural Network
================

Neuron Model
-------------
根据neuron的activation function不同，常见的neuron model有:

Sigmoid Neuron
^^^^^^^^^^^^^^^^

Rectified Linear Neuron
^^^^^^^^^^^^^^^^^^^^^^^^^

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