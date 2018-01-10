Variable
=========

what is tf.Variable
--------------------
本质上讲，variables是tensorflow operation

creating/using/sharing variables
---------------------------------
无论是creating, using, 还是sharing variables，都会使用同一个函数，tf.get_variable()。这个函数使用了一个关键参数name，可以回顾graph/operation/name章节中的内容。

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
设定variable的name scope时，使用了设定operation name scope时不一样的函数(见graph/operation/name章节)。

