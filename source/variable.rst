Variable
=========

what is tf.Variable
--------------------
本质上讲，variables是tensorflow operation

creating/using/sharing variables
---------------------------------
无论是creating, using, 还是sharing variables，都推荐使用同一个函数，tf.get_variable()。这个函数使用了一个关键参数name，可以回顾graph/operation/name章节中的内容。

tf.get_variable()干了两件事，都和tf.variable_scope()有关：

1. prefixes the name with the current variable scope and
2. performs reuse checks（reuse是tf.variable_scope()的一个参数）

.. code-block:: python
  :linenos:

  #tf.get_variable()必须和tf.variable_scope()一起使用
  with tf.variable_scope('vs'):
    v = tf.get_variable('ws', [2,2,3,32])
  print(v.name) #=>vs/ws:0

initialization
----------------
1. 最容易想到的初始化方法：

.. code-block:: python
  :linenos:

  #run the variable's initializer operation. For example:
  session.run(my_variable.initializer)

2. 如果定义的Variable太多，利用了collection的概念，效率更高的方法来初始化

.. code-block:: python
  :linenos:

  #tf.global_variables_initializer(). This function returns a single operation responsible #for initializing all variables in the tf.GraphKeys.GLOBAL_VARIABLES collection.
  session.run(tf.global_variables_initializer())

3. Most high-level frameworks such as tf.contrib.slim, tf.estimator.Estimator and Keras 
automatically initialize variables for you before training a model.


4. 查询还没有初始化的variable

.. code-block:: python
  :linenos:

  print(session.run(tf.report_uninitialized_variables()))

name
------
Variable从本质上是一个operation，在设定variable的name scope时，使用了和设定operation name scope时不一样的函数(见graph/operation/name章节)。

在某个tf.Graph中，不应出现同名的variable。

-从variable最终的名字上看，避免出现重名

使用tf.variable_scope()，给variable name加上prefix，就算创建variable的name参数相同也没事，避免重名

-传给tf.get_variable()或者tf.Variable()的name参数是否允许相同

.. code-block:: python
  :linenos:

  #graph会自动处理name参数相同的问题
  v = tf.Variable( [2,2,3,32],name='weights')
  v1 = tf.Variable( [2,2,3,32],name='weights')
  print(v.name) #=>weights_4:0
  print(v1.name) #=>weights_5:0

  #tf.Variable()并没有检查variable_scope()的reuse参数
  with tf.variable_scope('vs'):
    v = tf.Variable( [2,2,3,32],name='weights')
    v1 = tf.Variable( [2,2,3,32],name='weights')
  print(v.name) #=>vs/weights:0
  print(v1.name) #=>vs/weights_1:0

  #tf.name_scope()对tf.Variable()还是起作用的
  with tf.name_scope('vs'):
    v = tf.Variable( [2,2,3,32],name='weights')
    v1 = tf.Variable( [2,2,3,32],name='weights')
  print(v.name) #=>vs_1/weights:0
  print(v1.name) #=>vs_1/weights_1:0

  #设置reuse参数后，tf.get_variable()的name参数可以相同，否则会报错
  with tf.variable_scope('vs', reuse=tf.AUTO_REUSE):
    v = tf.get_variable('ws3', [2,2,3,32])
    #发现了同名variable,vs/ws3:0,然后检查reuse产生可用,于是返回了上一步的v
    v1 = tf.get_variable('ws3', [2,2,3,32]) 
  print(v.name) #=>vs/ws3:0
  print(v1.name) #=>vs/ws3:0

sharing
--------

使用variable name来分辨不同的variable，所谓sharing，就是返回同名的已经创建的variable。

Using tf.Variable()
^^^^^^^^^^^^^^^^^^^^
如果使用tf.Variable(), 即使传入的name参数相同，sharing variable也无从谈起。

.. code-block:: python
  :linenos:

  #graph会自动处理name参数相同的问题
  v = tf.Variable( [2,2,3,32],name='weights')
  v1 = tf.Variable( [2,2,3,32],name='weights')
  print(v.name) #=>weights_4:0
  print(v1.name) #=>weights_5:0

  #tf.Variable()并没有检查variable_scope()的reuse参数
  with tf.variable_scope('vs'):
    v = tf.Variable( [2,2,3,32],name='weights')
    v1 = tf.Variable( [2,2,3,32],name='weights')
  print(v.name) #=>vs/weights:0
  print(v1.name) #=>vs/weights_1:0

Using tf.get_variable()
^^^^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: python
  :linenos:

  #设置reuse参数后，tf.get_variable()可以重用同名的已经创建的variable
  with tf.variable_scope('vs', reuse=tf.AUTO_REUSE):
    v = tf.get_variable('ws3', [2,2,3,32])
    #发现了同名variable,vs/ws3:0,然后检查reuse可用,于是返回了上一步的v
    v1 = tf.get_variable('ws3', [2,2,3,32]) 
  print(v.name) #=>vs/ws3:0
  print(v1.name) #=>vs/ws3:0
