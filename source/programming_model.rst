tf的编程模型(programming model)
================================
tf has two programming model:

1. Low-level programming model

2. Higher-level APIs

.. _programming-model:

Low-level Programming Model
---------------------------------

该模型有两层：

1. Graph level

2. Session and evaluation level

Graph level
^^^^^^^^^^^^^^

This level is to design operations which are organised as a Graph. And your Graph holds operations and tensors, not values.

.. code-block:: python
  :linenos:

  a = tf.add(2, 2)
  print(a) # => Tensor("Add:0", shape=(), dtype=int32)
  print(type(a)) #=> <class 'tensorflow.python.framework.ops.Tensor'>

Session and evaluation level
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
When you start what is called a Session, you actually create a new scope for your program where operations can “happens”. This is where you can run or evaluate operations and tensors. And when you do so, results start to unravel: tensors get filled with real values, operations get computed, results are obtained, functions converge, etc.

But as soon as you get out of the scope of your Session, the Graph returns to its static and quite boring state.

.. code-block:: python
  :linenos:

  a=tf.add(2,2)
  with tf.Session() as sess:
    print(a)  #=>Tensor("Add_5:0", shape=(), dtype=int32)
    print(sess.run(a)) #=>4
    print(type(sess.run(a))) #=><class 'numpy.int32'>
    print(a)  #=>Tensor("Add_5:0", shape=(), dtype=int32)

Higher-level APIs
-------------------
:ref:`TF-slim <tf-slim>`