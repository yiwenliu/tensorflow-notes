CNN
=====

.. _component_of_a_convoluntional_layer:

Graph（网络结构）
--------------------
典型的CNN由如下三个部分级联而成：

1. 卷积层
2. 全连接层，得到固定长度的特征向量
3. softmax，进行分类

定义Graph的流程
^^^^^^^^^^^^^^^^^^
1. 用tf.placeholder()定义整个CNN **4-D input** of shape [batch, in_height, in_width, in_channels]
2. 用tf.Variable()定义卷积层使用的 **4-D weight** of shape [filter_height, filter_width, in_channels, out_channels] and **1-D bias** of shape [out_channels]
#. 定义单卷积层的组件: conv operation, pool operation...
#. 用单层（第1层）的参数，包括卷积核的尺寸和个数，还有卷积和池化的步长等初始化weight、bias和conv/pool operation，把input等连起来，定义单层卷积层
#. 把上一层的output作为input，重复上一步，定义下一层卷积
#. 定义全连接层、Dropout层和Softmax层

卷积层
-------
每个卷积层要完成两个操作：

1. 抽取特征
2. 抗形变

How to 抽取特征
^^^^^^^^^^^^^^^^

How to 抗形变
^^^^^^^^^^^^^^^^

1. 激活函数

激活函数本身就是neuron model的一部分，不是从属于CNN或者是RNN。

2. 池化

池化简单来说就是将输入图像切块，大部分时候我们选择不重叠的区域，再取切分区域中所有值的均值或最大值作为代表该区域的新值，放入池化后的二维信息图中。

池化操作和卷积操作很类似，都存在kernel_size和stride两个重要参数，池化可以理解为卷积核都为1的卷积。

卷积层的层数
^^^^^^^^^^^^^
较浅的卷积层（靠前的）的感受域比较小，学习感知细节部分的能力强，较深的隐藏层 (靠后的)感受域相对较大，适合学习较为整体的、相对更宏观一些的特征。

Dropout
----------
Usage
^^^^^^^^
在《tensorflow实战》ch5，Dropout层用在了全连接层的后面，softmax之前。

Intro
^^^^^^^
下面的链接很好的解释了Dropout的概念及其本质。
http://www.jianshu.com/p/c9f66bc8f96c

Train Model
-------------

cost Function
^^^^^^^^^^^^^^
CNN的最后一层的实现及其意义决定了cost Function

- 最后一层是softmax，loss function就是cross entropy

Optimization Algorithm
^^^^^^^^^^^^^^^^^^^^^^^^