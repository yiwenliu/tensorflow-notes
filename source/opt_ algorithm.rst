Optimization Algorithm
=========================

What to Optimize
-------------------
最优算法就是寻找cost function最小值的算法

Evaluation the Algorithm
--------------------------

.. _convergence-speed:

Convergence Speed
^^^^^^^^^^^^^^^^^^^

该快的时候快，该慢的时候慢，例如：

- move quickly in directions with small but consistent(一致性) gradients
- move slowly in directions with big but inconsistent gradients.

2. **whether the learning goes wrong: convergent or not, global or local minimum**
3. Generalization(Andrew Ng), underfit(high bias) or overfit(high variance)

Convergent or Not
^^^^^^^^^^^^^^^^^^
Plot J :subscript:`train` (θ) as a function of the number of iterations of gradient descent.

- 坐标图中只需要一条曲线就可以判断
- 不同的learning method在曲线取样点的选择上有一些不同（From Andrew Ng Week 10）

Generalization
^^^^^^^^^^^^^^^
在学习（迭代）的过程中，需要同时画两条曲线，通过对比才能判断。

What to concern
-----------------
算法需要考虑的“因素”包括：

- :ref:`learning method <learning-method>`
- initialization of the weights
- :ref:`the stride of the weight movment <learning-rate>`, 即learning rate
- :ref:`direction of the weight movment <direction-descent>`, e.g. steepest descent的梯度反方向
- regularization, λ(Andrew Ng)

.. _learning-method:

Learning method
^^^^^^^^^^^^^^^^^^
对于神经网络而言，依据训练集的数据特点，有四种常用的学习方法：

full-batch method
++++++++++++++++++

适用于small **datasets** (e.g. 10,000 cases) or bigger datasets without much redundancy

.. _mini-batch-method:

mini-batch method
+++++++++++++++++++++

(from Hilton)适用于big, redundant **datasets**, e.g. CNN中使用的图片数据。最好使用 **big mini-batches** ,不仅计算效率高、速度快，而且也满足一些fancy optimiaztion algorithm的需要。

还可以从 :ref:`learning curve <large-scale-data-ps>` 的角度来理解mini-batch的可行性

相关概念：

- epoch
- iteration
- batch size
- number of batches

当一个完整的数据集通过了神经网络一次并且返回了一次，这个过程称为一个 epoch。

迭代是 batch 需要完成一个 epoch 的次数。记住：在一个 epoch 中，batch 数和迭代数是相等的。比如对于一个有 2000 个训练样本的数据集。将 2000 个样本分成大小为 500 的 batch，那么完成一个 epoch 需要 4 个 iteration。

stochastic method
+++++++++++++++++++

Frome Andrew Ng 10th Week，下图来自于Andrew Ng 10th-week

.. image:: img/nn-4.png

online method
++++++++++++++++

(From Andrew Ng 10th week)The online learning setting allows us to model problems where we have a continuous flood or a continuous stream of data coming in and we would like an algorithm to learn from that. 

Online method的算法和stochastic method类似，但是，前者不保存training examples，用过之后即丢弃。

.. _learning-rate:

Learning Rate
^^^^^^^^^^^^^^^^^
How α influence the learning 
++++++++++++++++++++++++++++++++
根据Gradient discent的公式，

.. image:: img/nn-3.png

learning rate的取值大小通过直接影响Weights，进而影响cost function的收敛性，无法兼顾学习速度和收敛结果(是否成功找到cost function的最小值)。

- weights在 **error surface** 中的移动方式，会影响收敛速度和收敛结果
- learning rate太大，就湮没了不同weights分量的梯度的不同，因为是 **learing-rate * gradient**，进而导致收敛速度降低


When&How to adjust α
+++++++++++++++++++++++
目前，有两种常用的调节learning rate的方法：

- 设置初值，根据learning speed再手工调节, e.g. :ref:`SGD <sgd-lr>` , Momentum, Nesterov Momentum
- 自适应, e.g. RMSProp, Adam, AdaGrad

Manual learning rate
+++++++++++++++++++++++

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
^^^^^^^^^^^^^^^^^^^^
The Direction of steepest descent
++++++++++++++++++++++++++++++++++++++
1. cost function的值下降最快的方向就是梯度的反方向。

2. 有两种gradient(From Hilton)

- small but consistent(一致性) gradients
- big but inconsistent gradients.

（quickly和slowly是如何量化的呢？——learning rate）

Other directions of cost function descent
++++++++++++++++++++++++++++++++++++++++++++

Instance 
----------
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