.. _ops:

Operations
------------

tf.Operation
^^^^^^^^^^^^^^^
In python, Class Operation

input&output of operations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
计算图中每一个节点都可以有任意多个输入和任意多个输出，每一个节点描述了一种运算操作，节点可以算是运算操作的实例化（instance）。

tensor OR `Tensor-like objects <https://www.tensorflow.org/programmers_guide/graphs#tensor-like_objects>`_

How to create an operation object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
下面的3种方法都可以设置name参数，但是，只有第3种方法可以指定collections参数。

1. a Python op constructor, such as tf.matmul(), tf.nn.conv2d()
2. tf.Graph.create_op()
3. 构造函数，such as tf.Variable()

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