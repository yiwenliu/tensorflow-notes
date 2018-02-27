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

  weight = tf.Variable(initial_value) #defaul is a regular variable
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

- tf

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

- slim

.. code-block:: python
  :linenos:

  input = ...
  net = slim.conv2d(input, 128, [3, 3], scope='conv1_1')

slim.conv2d(), `tutorial <https://www.tensorflow.org/api_docs/python/tf/contrib/layers/conv2d>`_ ,中的几个参数包含了使用tf.nn.conv2d()时的操作：

- activation_fn=tf.nn.relu, # 用于激活函数的指定，默认的为ReLU函数
- weights_initializer=initializers.xavier_initializer(),
- weights_regularizer=None,
- biases_initializer=tf.zeros_initializer(),

.. _arg-scope:

Scopes
^^^^^^^^
arg_scope.py模块，涉及到一个新的 :ref:`scope mechanisms <scope>` 的概念，在 `github page <https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim#scopes>`_ 中详述了arg_scope()函数的意义和用法，另可见 `def arg_scope() in tf tutorial <https://www.tensorflow.org/api_docs/python/tf/contrib/framework/arg_scope>`_

Training Models
-----------------

Fine-Tuning Existing Models
----------------------------

Evaluating Models
---------------------