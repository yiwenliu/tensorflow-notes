Dataflow Programming Model
============================
What is Dataflow Programming
-----------------------------
models a program as a directed graph of the data flowing between operations

Why dataflow graph
-------------------
- parallel computing
- large-scale data processing

.. _4-dataflow:

Four Concepts about Dataflow
-----------------------------
`Details of four major concepts on Google Cloud Platform <https://cloud.google.com/dataflow/model/programming-model>`_

- Pipelines, 等同于tf中的subgraph
- PCollections, 等同于tf中的tensor
- Transforms, 等同于tf中的node
- I/O Sources and Sinks(input source and output sink)，等同于tf中的inputing and outputing data

此四者的关系如下图所示，可见Dataflow就是由一个一个pipeline组成的：

.. image:: img/pipeline.png

.. _pipeline-stage:

Look inside a pipeline
-------------------------
1. composition

从上一小节看出，a pipeline等同于tf中的subgraph，有四个组件：input, transform, pcollections, output。Each pipeline represents a single, potentially repeatable job, from start to finish, in the Dataflow service.

2. stages

pipeline的stages类似于tf中的collections，pipeline中的transforms are grouped into different stages and transforms of the same stage are executed in parallel.

3. connections between stages

在用QueueRunner实现的tf "input pipeline"中，stages之间由queue来连接。

4. direction

一个stage中的所有transform执行成功后，才执行下一个stage的transform

.. _pipeline-design:

Pipeline Design Principles
----------------------------
对照上一小节“Four Concepts about Dataflow”，详见 `Pipeline Design Principles on Google Cloudplatform <https://cloud.google.com/dataflow/pipelines/design-principles>`_, 请结合文中提及的四个基本问题来认识pipeline的五种形态。

Pipe and Queue in Python
---------------------------
有必要了解一些python实现pipe和queue的基本知识。在2016年实现的亚太主要英文媒体的舆情监控系统中其实已经用到了multiprocessing.Queue。

pipe和queue是“进程间通信”的两种方式，隐含了进程间的协作关系，“生产者”和“消费者”、“同步”和“互斥”的意义和实现，例如，queue满了后，发送进程会阻塞。所以应优先考虑Pipe和Queue，避免使用Lock/Event/Semaphore/Condition等同步方式 (因为它们占据的不是用户进程的资源)。

由package multiprocessing实现， `on github <https://github.com/python/cpython/tree/3.5/Lib/multiprocessing/>`_, `on docs <https://docs.python.org/3.5/library/multiprocessing.html#pipes-and-queues>`_

+------------+-------------------+-------------+
|            | pipe              | queue       |
+------------+-------------------+-------------+
| 实现基础   | socket            | pipe        |
+------------+-------------------+-------------+
| 实现方式   | def Pipe()        | class Queue |
+------------+-------------------+-------------+
| 容量       | 1                 | maxsize     |
+------------+-------------------+-------------+
| 传输方向   | duplex/single;    | 无          |
|            | send end&recv end |             |
+------------+-------------------+-------------+
| 关联进程数 | 一端只能一个进程  | 多进程读写  |
+------------+-------------------+-------------+

`This link <http://www.cnblogs.com/vamei/archive/2012/10/12/2721484.html>`_ 中的“Pipe和Queue”部分有例程可以参考。

.. _queue:

Threading and Queues in TF
-----------------------------
Attention
^^^^^^^^^^^^
`Threading and Queues <https://www.tensorflow.org/api_guides/python/threading_and_queues>`_

In versions of TensorFlow before 1.2, we recommended using multi-threaded, queue-based input pipelines for performance. Beginning with TensorFlow 1.4, however, we recommend using the tf.data module instead. The tf.data module offers **an easier-to-use interface for constructing efficient input pipelines**. Furthermore, we've stopped developing the old multi-threaded, queue-based input pipelines.

What's Queue
^^^^^^^^^^^^^^
- TensorFlow's queues are implemented using nodes in the computation graph. 
- A queue is a stateful node, like a variable: other nodes can modify its content. So, there are nodes which can enqueue new items in to the queue, or dequeue existing items from the queue. 

下图是a graph that takes an item off the queue, adds one to that item, and puts it back on the end of the queue. 

.. image:: img/IncremeterFifoQueue.gif

说明：

  1. Enqueue, EnqueueMany, and Dequeue are special nodes which are created by calling methods on a queue object (e.g. q.enqueue(...)). They take a pointer to the queue instead of a normal value, allowing them to mutate its state. 
  2. 无论通过什么其他的api来使用queue，都要用到上图中的几个node。

- Queue's coordinator: a queue will block any step that attempts to dequeue from it when it is empty, or enqueue to it when it is full. 
- TensorFlow implements `several classes of queue <https://www.tensorflow.org/api_guides/python/io_ops#Queues>`_. The principal difference between these classes is the order that items are removed from the queue. 


Apache Beam
--------------
2016年2月谷歌高调宣布将Apache Beam（原名Google DataFlow）贡献给Apache基金会孵化，Apache Beam被认为是继MapReduce，GFS和BigQuery等之后，谷歌在大数据处理领域对开源社区的又一个非常大的贡献。

.. image:: img/beam.jpg