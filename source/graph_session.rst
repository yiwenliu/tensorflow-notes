Graph
========

What is a tf.Graph
------------------------
TensorFlow provides **a default graph** that is an implicit argument(隐式参数) to all API functions in the same context.

tf.Graph包含两个相关联的信息：

1. Graph structure
^^^^^^^^^^^^^^^^^^^^

2. Graph collections
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
其实就是一个dict，associate a list of objects with a key，这里的object可能指的就是node/operation。 

collections的意义：Because disconnected parts of a TensorFlow program might want to create **variables**, it is sometimes useful to have a single way to access all of them 

例如，tf.Variable()这个API就实现了上述两个信息：

1. Executing v = tf.Variable(0) adds to the graph a tf.Operation that will store a writeable tensor value that persists between tf.Session.run calls.

2. when you create a tf.Variable, it is added by default to collections representing "global variables" and "trainable variables".

Operations
------------
input
^^^^^^
tensor OR `Tensor-like objects <https://www.tensorflow.org/programmers_guide/graphs#tensor-like_objects>`_

output
^^^^^^^

graph context
^^^^^^^^^^^^^^^
1. default graph

TensorFlow provides a "default graph" that is implicitly passed to all API functions in the same context.

2. 多graph时设置graph context

`programming with multiple graphs <https://www.tensorflow.org/programmers_guide/graphs#programming_with_multiple_graphs>`_

name 
^^^^^
1. 意义

tf.Graph中的operation以及其返回的tensor都要有唯一的名字。

和collection不同，name和Operations是一一对应的关系。

2. 定义

TensorFlow automatically chooses a unique name for each operation in your graph（默认name）。
但是，有两种方式 `<https://www.tensorflow.org/programmers_guide/graphs#naming_operations>`_ 可以改变这个默认的name。

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

Tensor
--------

Collection
-----------

Executing/Evaluation
----------------------
我们构建的graph实际上就是一个client program，其和C++ runtime之间的连接由tf.Session来实现。

Session
=========

create a session
-----------------
Session的一头是Graph(client program)，一头是C++ runtime。

.. code-block:: python
  :linenos:

  # Create a default in-process session.
  with tf.Session() as sess:
  # ...

  # Create a remote session.
  with tf.Session("grpc://example.org:2222"):
  # ...

  g_1 = tf.Graph()
  with g_1.as_default():
  # Sessions created in this scope will run operations from `g_1`.
    sess_1 = tf.Session()

Using tf.Session.run()
-----------------------
