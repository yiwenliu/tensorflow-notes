TF-Slim
=========

create variables
------------------

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
----------------------

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

定义一个卷积层
--------------

- tf
- slim