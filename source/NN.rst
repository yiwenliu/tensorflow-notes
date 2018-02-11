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

Look inside Optimization Algorithm
------------------------------------
Sum.
^^^^^^
最优算法就是寻找cost function最小值的算法，算法需要考虑的“因素”包括：

- learning method
- initialization of the weights
- :ref:`the stride of the weight movment <learning-rate>`, 即learning rate
- :ref:`direction of the weight movment <direction-descent>`, e.g. steepest descent的梯度反方向
- regularization, λ(Andrew Ng)

算法中所有考虑因素的“落脚点”只有三个：

.. _convergence-speed:

1. **convergence speed**

该快的时候快，该慢的时候慢，例如：

- move quickly in directions with small but consistent(一致性) gradients
- move slowly in directions with big but inconsistent gradients.

2. **whether the learning goes wrong: global minimum or local minimum**
3. Generalization(Andrew Ng)

.. _learning-method:

Learning method
^^^^^^^^^^^^^^^^^^
对于神经网络而言，依据训练集的数据特点，有三种常用的学习方法：

- full-batch method

适用于small **datasets** (e.g. 10,000 cases) or bigger datasets without much redundancy

.. _mini-batch-method:

- mini-batch method

适用于big, redundant **datasets**, e.g. CNN中使用的图片数据。

还可以从 :ref:`learning curve <large-scale-data-ps>` 的角度来理解mini-batch的可行性

最好使用 **big mini-batches** ,不仅计算效率高、速度快，而且也满足一些fancy optimiaztion algorithm的需要。

- online method

update weights after each case，下图来自于Andrew Ng 10th-week

.. image:: img/nn-4.png

.. _learning-rate:

Learning Rate
^^^^^^^^^^^^^^^
learning rate的大小对learning的影响
++++++++++++++++++++++++++++++++++++
根据Gradient discent的公式，

.. image:: img/nn-3.png

learning rate的取值大小通过直接影响Weights，进而影响cost function的收敛性，无法兼顾学习速度和收敛结果(是否成功找到cost function的最小值)。

- weights在 **error surface** 中的移动方式，会影响收敛速度和收敛结果
- learning rate太大，就湮没了不同weights分量的梯度的不同，因为是 **learing-rate * gradient**，进而导致收敛速度降低


调节learning rate的方法
+++++++++++++++++++++++++
目前，有两种常用的调节learning rate的方法：

- 设置初值，根据learning speed再手工调节, e.g. :ref:`SGD <sgd-lr>` , Momentum, Nesterov Momentum
- 自适应, e.g. RMSProp, Adam, AdaGrad

Manual learning rate
+++++++++++++++++++++

+-------------+-------------------------------------------------------------------+--------------+-----------------+
|             |                               error                               | convergence  |   convergence   |
|             |                                                                   |     speed    |      result     |
+-------------+-------------------------------------------------------------------+--------------+-----------------+
| turn down α | reduce the random fluctuations(随机波动)in the error              | slower       | get a quick win |
|             | due to the different gradients on different mini-batch            |              |                 |
+-------------+-------------------------------------------------------------------+--------------+-----------------+
| turn up α   | weighs slosh to and fro(来回摇摆) across the ravine(峡谷)，如下图 | quick        | failed          |
+-------------+-------------------------------------------------------------------+--------------+-----------------+

.. image:: img/nn-2.png

Adaptive learning rate
++++++++++++++++++++++++
依据是gradient的特征

- gradient符号正负号的转换
- gradient的量值

.. _direction-descent:

Descent Direction
^^^^^^^^^^^^^^^^^^^
The Direction of steepest descent
++++++++++++++++++++++++++++++++++
1. cost function的值下降最快的方向就是梯度的反方向。

2. 有两种gradient(From Hilton)

- small but consistent(一致性) gradients
- big but inconsistent gradients.

（quickly和slowly是如何量化的呢？——learning rate）

Other directions of cost function descent
+++++++++++++++++++++++++++++++++++++++++++++

Instance of the Optimization Algorithms
------------------------------------------
一个算法可能就出自一篇论文。

SGD
^^^^^^
1. 随机梯度下降，Stochastic Gradient Descent，又可以称为mini-batch gradient descent
2. 使用一小部分样本进行训练
#. MNIST training set只有55000个样本，下面的例子却使用总数为100万的训练样本数量

.. code-block:: python
  :linenos:

  #return an operation
  train_step = tf.train.GradientDescentOptimizer(learning-rate).minimize(loss-function)
  for i in range(20000):
    batch = mnist.train.next_batch(50)
    train_step.run(feed_dict={x:batch[0], y_:batch[1]})

.. _sgd-lr:

4. manual adjust **learning rate** to mini-batch gradient descent

- if the error keeps getting worse or oscillates wildly, **reduce** the learning rate
- towards the end of learning it nearly always helps to **turn down** the learning rate
- when error stops decreaseing, **turn down** the learning rate
- if the error is falling fairly consistently bust slow, **increase** the learning rate
    

BGD
^^^^^
batch gradient descent，传统的梯度下降每次使用全部样本进行训练

Momentum
^^^^^^^^^^^
1. 在求∇W时，没有采用"steepest descent"（问题是，没有沿着梯度的方向，为什么还能加速？）
2. Hilton says(lecture 6c) it can speed up mini-batch learning, 但是代价是引入了一个新的“动量衰减参数”

Rprop
^^^^^^^
1. Hilton lecture6
2. use a full-batch method
3. use adaptive learning rates

引入了一个新的参数——local gain, g, α->α*g

Rmsprop
^^^^^^^^^
1. Hilton lecture6
2. use mini-batch method
#. use adaptive learning rates

Adam
^^^^^
1. use momentum
2. use mini-batch method
3. adaptive learning rates

使用NN的一般流程
------------------
1. 准备数据

2. 根据training data set, 设计网络结构—— :ref:`Graph Level <programming-model>`

- :ref:`How To Define CNN Graph <component_of_a_convoluntional_layer>`

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