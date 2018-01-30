Neural Network
================

使用NN的一般流程
------------------
1. 准备数据

2. 根据training data set, 设计网络结构—— :ref:`Graph Level <programming-model>`

- :ref:`How To Define CNN Graph <component_of_a_convoluntional_layer>`

3. 定义loss function

- loss function往往由整个CNN中最后一层的形态和意义来决定
- 最好加入 penalty factor——λ，以免过拟合

4. 定义使loss function最小化的优化器

需要设置一个参数learning rate，用于余梯度下降时控制下降的速率。

用NN解决分类问题
-----------------
无论是CNN还是RNN，如果是分类模型，最后一层也同样是Softmax Regression，使用cross-entropy作为loss function。

用NN解决回归问题
-----------------

Neuron Model
--------------
常见的神经元模型有

Sigmoid Neuron
^^^^^^^^^^^^^^^^

Rectified Linear Neuron
^^^^^^^^^^^^^^^^^^^^^^^^^

Linear Neuron
^^^^^^^^^^^^^^^

具体应用场景
--------------
图像语义分割
^^^^^^^^^^^^^
图像的语义分割是像素级别的分类问题

《语义分割中的深度学习方法全解：从FCN、SegNet到各代DeepLab》
https://zhuanlan.zhihu.com/p/27794982

《十分钟看懂图像语义分割技术》
https://www.leiphone.com/news/201705/YbRHBVIjhqVBP0X5.html