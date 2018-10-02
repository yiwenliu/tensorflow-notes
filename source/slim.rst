.. _tf-slim:

TF-Slim
=========
What's slim
-------------
`slim's main page on github <https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim>`_

TF-Slim is a library that makes building, training and evaluation neural networks simple.

TF-Slim is composed of several parts which were design to exist independently。这些component，模块(.py文件)或者是包(文件夹)，分别实现对神经网络的building, training and evaluation。

Defining Model
----------------
slim components中用于定义模型的模块或者包有：

- variables.py
- layers.py
- arg_scope.py

create variables
^^^^^^^^^^^^^^^^^^^

- tf

.. code-block:: python
  :linenos:

  weights = tf.Variable(tf.truncated_normal(shape=[10,10,3,3], stddev=0.1))
  weight_loss = tf.multiply(tf.nn.l2_loss(weights), 0.05, name='weight_loss')
  tf.add_to_collection('losses', weight_loss)

- slim

.. code-block:: python
  :linenos:

  weights = slim.variable('weights',
                           shape=[10, 10, 3 , 3],
                           initializer=tf.truncated_normal_initializer(stddev=0.1),
                           regularizer=slim.l2_regularizer(0.05),
                           device='/CPU:0')

variable collections
^^^^^^^^^^^^^^^^^^^^^^

- tf

Note that in native TensorFlow, there are two types of variables:

1. regular variables and
2. local (transient) variables.

The vast majority of variables are regular variables: once created, they can be saved to disk using a saver. Local variables are those variables that only exist for the duration of a session and are not saved to disk.

.. code-block:: python
  :linenos:

  weight = tf.Variable(initial_value) #defaul collection is GraphKeys.GLOBAL_VARIABLES and is a regular variable
  #使用不同的collection参数，这个Variable object就有很多不同了
  count = tf.Varialbe(initial_value, collections=LOCAL_VARIABLES) 

- slim

.. code-block:: python
  :linenos:

  '''
  类别1、Model Variables
  '''
  weights = slim.model_variable('weights',
                   shape=[10, 10, 3 , 3],
                   initializer=tf.truncated_normal_initializer(stddev=0.1),
                   regularizer=slim.l2_regularizer(0.05),
                   device='/CPU:0')
  model_variables = slim.get_model_variables()

  '''
  类别2、Regular variables,
  For example, the global_step is a variable using during learning and evaluation but it is not actually part of the model.
  So, regularizer and device properties are unnecessary
  '''
  my_var = slim.variable('my_var',shape=[20, 1],initializer=tf.zeros_initializer())
  regular_variables_and_model_variables = slim.get_variables()

How does this work? When you create a model variable via TF-Slim's layers or directly via the slim.model_variable function, TF-Slim adds the variable to a the tf.GraphKeys.MODEL_VARIABLES collection.

Create a Layer
^^^^^^^^^^^^^^^^^^^
由layers.py模块实现。

卷积
+++++++

1. tf

.. code-block:: python
  :linenos:

  input = ...
  with tf.name_scope('conv1_1') as scope:
    kernel = tf.Variable(tf.truncated_normal([3, 3, 64, 128],
              dtype=tf.float32,stddev=1e-1), name='weights')
    conv = tf.nn.conv2d(input, kernel, [1, 1, 1, 1], padding='SAME')
    biases = tf.Variable(tf.constant(0.0, shape=[128], dtype=tf.float32),
                       trainable=True, name='biases')
    bias = tf.nn.bias_add(conv, biases)
    conv1 = tf.nn.relu(bias, name=scope)

2. slim

.. code-block:: python
  :linenos:

  input = ...
  net = slim.conv2d(input, 128, [3, 3], scope='conv1_1')

slim.conv2d(), `tutorial <https://www.tensorflow.org/api_docs/python/tf/contrib/layers/conv2d>`_ ,中的几个参数包含了使用tf.nn.conv2d()时的操作：

- activation_fn=tf.nn.relu, # 用于激活函数的指定，默认的为ReLU函数
- weights_initializer=initializers.xavier_initializer(),
- weights_regularizer=None,
- biases_initializer=tf.zeros_initializer(),

3. 两者的区别

- 在slim.conv2d()的参数列表中，初始化weights矩阵时，并未像tf那样指定shape，因为slim.conv2d()设定了num_outputs和kernel_size两个参数，就没有必要再设定weights的shape了，这样做，反而更明了。
- 在slim.conv2d()的参数列表中包括了bias，如此，就不必像tf那样初始化bias时要指定其shape
- 在slim.conv2d()的参数列表中包括了神经元的“激活函数”，如此，就不必像tf那样“显示调用”tf.nn.bias_add()以及tf.nn.relu()了
- slim.conv2d()有weights_regularizer参数，可以和tf处理regularization的方式——《tf实战》p86——进行比较，简单不少
- slim.conv2d()的scope参数——Optional scope for variable_scope.就不用像tf那样，显示调用with tf.variable_scope('xxx')了。

池化
++++++

.. code-block:: none
    :linenos:

    #tf
    tf.nn.max_pool(conv1, ksize=[1,3,3,1], strides=[1,2,2,1])
    #slim
    slim.max_pool2d(conv1, kernel_size=[3,3], stride=2)

比较发现：

- slim的shape参数的设置比较容易理解，而tf设置的4-d shape很容易迷惑

.. _arg-scope:

Scopes
^^^^^^^^
tf中所有socpe的概念都是为了在其范围内定义一个统一的参数，使代码更加简洁。

arg_scope.py模块，涉及到一个新的 :ref:`scope mechanisms <scope>` 的概念——allows a user to specify one or more operations and a set of arguments which will be passed to each of the operations defined in the arg_scope（见函数原型）. 

在 `github page <https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim#scopes>`_ 中详述了如下内容：

- 引入arg_scope()函数的原因
- 示例程序

另可见 `def arg_scope() in tf tutorial <https://www.tensorflow.org/api_docs/python/tf/contrib/framework/arg_scope>`_

函数原型如下：

.. code-block:: python
    :linenos:

    slim.arg_scope([a list of operations], arg1=xxx, arg2=xxx, ...)

Training Models
-----------------

Fine-Tuning Existing Models
----------------------------

Evaluating Models
---------------------