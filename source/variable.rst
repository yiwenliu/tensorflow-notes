Variable
=========

what is tf.Variable
--------------------
本质上讲，variables是tensorflow :ref:`operation <ops>`

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


.. _variable-name:

name
------
Variable从本质上是一个operation，在设定variable的name scope时，
使用了和设定 :ref:`operation name scope <ops-name>` 时不一样的函数。

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

Saving&Restoring
-------------------
Procedure
^^^^^^^^^^^^
Saving and restoring model is more complicated than variables.

Tensorflow支持检查点(checkpoint)的保存和恢复，每一个variable node都会链接一个save node，每隔几轮迭代就会保存一次数据到持久化的存储系统。同样，每一个variable node都会链接一个restore node，在每次重启时会被调用并恢复数据。

**The save and restore ops** are added by tf.train.Saver constructor to the graph for all, or a specified list, of the variables in the graph. 

.. code-block:: python
  :linenos:

  sess = tf.Session()
  # Add ops to save and restore all the variables
  saver = tf.train.Saver(max_to_keep=0)
  for step in range(MAX_STEP):
    ...
    #@prefix: 必须包含路径名, 例如在MTCNN中的"data/MTCNN_model/PNet_landmark/PNet"
    saver.save(sess, prefix, global_step=epoch*2)

从上述代码看出，

- 初始化一个saver object，就自动给varaible node加上了save node & restore node，这个过程的Graph图示可以参见 :ref:`write event file <write-event-file>`
- save()动作是在BP过程之外单独执行的，虽然没有显示调用session.run()，但是在def save()的 `source code <https://github.com/tensorflow/tensorflow/blob/r1.6/tensorflow/python/training/saver.py>`_ 中调用了它, line1652

Result
^^^^^^^^^
在MTCNN训练完PNet中，每次执行saver.save()生成三个文件：PNet-8.meta, PNet-8.index, PNet-8.data-00000-of-00001。最后，还有一个名为checkpoint的单独的文件。

 If the saver is sharded(分片), this string(path prefix used for the checkpoint files) ends with: '-?????-of-nnnnn' where 'nnnnn' is the number of shards created. 

写了两个文件：

1. checkpoints file

TensorFlow saves variables in binary checkpoint files that, roughly speaking, map variable names to tensor values.

2. a protocol buffer file