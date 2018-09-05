Graph
========

What is a tf.Graph
------------------------
In python, Graph is a class object, Class Graph.

TensorFlow provides **a default graph** that is an implicit argument(隐式参数) to all API functions in the same context.

tf.Graph包含两个相关联的信息：

Graph structure
^^^^^^^^^^^^^^^^^

Graph collections
^^^^^^^^^^^^^^^^^^^
1. 本质就是一个dict，associate **a list of objects** with a **key**，就是说，一个key对应着一堆objects，这里的objects指的就是 **tf.Operation objects and tf.Tensor objects** 

2. 意义就是可以批量操作ops&tensors. Because disconnected parts of a TensorFlow program might want to create **variables**, it is sometimes useful to have a single way to access all of them。

在任意位置，任意层次都可以创造对象，存入相应collection中；创造完成后，统一从一个collection中取出一类变量，施加相应操作。

例如，tf.Variable()这个API就实现了上述两个信息：

- Executing v = tf.Variable(0) adds to the graph a tf.Operation that will store a writeable tensor value that persists between tf.Session.run calls.

- when you create a tf.Variable, it is added by default to collections representing "global variables" and "trainable variables".

3. `tf.GraphKeys <https://www.tensorflow.org/versions/r0.12/api_docs/python/framework/graph_collections#GraphKeys>`_ 包含了所有默认的集合名称。

4. 实现方式就是新建ops object时，传入 **collections** 参数。

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

