Graph
========

What is a tf.Graph
------------------------
TensorFlow provides **a default graph** that is an implicit argument(隐式参数) to all API functions in the same context.

tf.Graph包含两个相关联的信息：

Graph structure
^^^^^^^^^^^^^^^^^

Graph collections
^^^^^^^^^^^^^^^^^^^
1. 本质就是一个dict，associate **a list of objects** with a **key**，就是说，一个key对应着一堆objects，这里的objects指的就是node/operation。 

2. 意义就是可以批量操作ops&tensors. Because disconnected parts of a TensorFlow program might want to create **variables**, it is sometimes useful to have a single way to access all of them。

在任意位置，任意层次都可以创造对象，存入相应collection中；创造完成后，统一从一个collection中取出一类变量，施加相应操作。

例如，tf.Variable()这个API就实现了上述两个信息：

- Executing v = tf.Variable(0) adds to the graph a tf.Operation that will store a writeable tensor value that persists between tf.Session.run calls.

- when you create a tf.Variable, it is added by default to collections representing "global variables" and "trainable variables".

3. `tf.GraphKeys <https://www.tensorflow.org/versions/r0.12/api_docs/python/framework/graph_collections#GraphKeys>`_ 包含了所有默认的集合名称。

4. 实现方式就是新建ops object时，传入 **collections** 参数。

.. _ops:

Operations
------------
input&output
^^^^^^^^^^^^^^^
计算图中每一个节点都可以有任意多个输入和任意多个输出，每一个节点描述了一种运算操作，节点可以算是运算操作的实例化（instance）。

tensor OR `Tensor-like objects <https://www.tensorflow.org/programmers_guide/graphs#tensor-like_objects>`_

graph context
^^^^^^^^^^^^^^^
1. default graph

TensorFlow provides a "default graph" that is implicitly passed to all API functions in the same context.

2. 多graph时设置graph context

`programming with multiple graphs <https://www.tensorflow.org/programmers_guide/graphs#programming_with_multiple_graphs>`_

.. _ops-name:

name 
^^^^^
1. 意义

tf.Graph中的operation以及其返回的tensor都要有唯一的名字。

和collection不同，name和Operations是一一对应的关系。

2. 定义

TensorFlow automatically chooses a unique name for each operation in your graph（默认name）。
但是，有两种方式 `<https://www.tensorflow.org/programmers_guide/graphs#naming_operations>`_ 可以改变这个默认的name。

- graph context. 因为each operation in a single graph must have a unique name. 

- tf.name_scope

.. code-block:: python
  :linenos:

  c_0 = tf.constant(0, name="c")  # => operation named "c"

  # Already-used names will be "uniquified".
  c_1 = tf.constant(2, name="c")  # => operation named "c_1"

  # Name scopes add a prefix to all operations created in the same context.
  with tf.name_scope("outer"):
    c_2 = tf.constant(2, name="c")  # => operation named "outer/c"

  # Name scopes nest like paths in a hierarchical file system.
    with tf.name_scope("inner"):
      c_3 = tf.constant(3, name="c")  # => operation named "outer/inner/c"

3.命名规则

`programming guid <https://www.tensorflow.org/programmers_guide/graphs#naming_operations>`_

running device
^^^^^^^^^^^^^^^^
1. 默认的设备

对于一个operation而言，tf会默认让其运行在某个设备上，即使是一台仅仅有一个GPU的机器，仍能把这个operation的计算置于CPU或者GPU。

2. 制定运行的设备
一个设备的specification如下：

/job:<JOB_NAME>/task:<TASK_INDEX>/device:<DEVICE_TYPE>:<DEVICE_INDEX>

其中，device:<DEVICE_TYPE>:<DEVICE_INDEX>好理解，那么，前半部分是什么意思？`distributed tensorflow <https://www.tensorflow.org/deploy/distributed>`_

Executing a graph
--------------------
我们构建的graph实际上就是一个client program，其和C++ runtime之间的连接由tf.Session来实现。

有三种方法执行一个图

- in a tf.Session.run(operation/tensor)
- operation.run()
- tensor.eval()

Saving and Restoring Models
------------------------------
`官方文档 <https://www.tensorflow.org/programmers_guide/saved_model#overview_of_saving_and_restoring_models>`_

