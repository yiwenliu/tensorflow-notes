.. _importing-data:

Importing Data
================

Backgroud
----------
在学习mtcnn实现的数据准备阶段，negative， positive, part和landmark的图片文件和标注文件都准备好了，接下来的问题是，如何把这些文件和数据提交给模型来运算呢？

- 在《tf实战》一书中使用的都是tf已经封装好的MNIST数据，直接import就行，并且提供了丰富的访问方法，见P46
- 自有的庞大数据该如何处理？

tf三种读取数据的方式
---------------------
Feeding
^^^^^^^^^
供给数据(Feeding)：定义tf.placeholder()，在tensorflow程序运行的每一步，让python代码来供给数据,e.g. opencv.imread()

Preloaded data
^^^^^^^^^^^^^^^^
预加载数据(Preloaded data)：如果数据量不太大，定义tf.Variable() or tf.constant(),可以在程序中定义常量或者变量来保存所有的数据。

Input Pipeline
^^^^^^^^^^^^^^^^
What's a pipeline
++++++++++++++++++
Pipeline不是pipe。

A pipeline represents a Directed Acyclic Graph(有向非循环图) of steps. 

A pipeline is a group of jobs that get executed in stages(batches).All of the jobs in a stage are executed in parallel (if there are enough concurrent Runners), and if they all succeed, the pipeline moves on to the next stage. If one of the jobs fails, the next stage is not (usually) executed. 

Stages的用法举例，可以参见 `stages <https://docs.gitlab.com/ee/ci/yaml/README.html#stages>`_

In the following image you can see that the pipeline consists of four stages (build, test, staging, production) each one having one or more jobs.

.. image:: img/pipelines.png

QueueRunner(Before TF 1.4)
+++++++++++++++++++++++++++++
QueueRunner(`Guide <https://www.tensorflow.org/api_guides/python/reading_data#_QueueRunner_>`_): **a queue-based input pipeline** reads the data from files at the beginning of a TensorFlow graph。 例如，从文件（tfrecord, cvs等文件）读取数据，建立输入管线（input pipeline）从文件中读取数据，其中tfrecord是tensorflow官方推荐的标准格式，tfrecord其实是一种数据存储形式，将图像数据和标签统一存储的二进制文件。

采用这种input pipeline的读取数据的流程见 `API Guide <https://www.tensorflow.org/api_guides/python/reading_data#_QueueRunner_>`_

.. image:: img/AnimatedFileQueues.gif

上图中涉及的步骤都有对应的API实现，例如，最后一步enqueue其实就是construct batch，可以参见 `How to write into and read from a TFRecords file in TensorFlow <http://www.machinelearninguru.com/deep_learning/tensorflow/basics/tfrecord/tfrecord.html>`_ 中"Read the TFRecords file"的部分。

tf.data API(From TF 1.4)
+++++++++++++++++++++++++++
The API can easily construct a complex input pipeline. (**preferred method**). This is **an improved version of the old input methods---feeding and QueueRunner**

从不同格式的文件读取数据
------------------------
显然，采用的是QueueRunner的Input Pipeline形式。

+--------------------------------------------+------------------------------+-------------------------------------+
|                  文件格式                  |            阅读器            |              记录解析器             |
+--------------------------------------------+------------------------------+-------------------------------------+
| CSV                                        | tf.TextLineReader()          | tf.decode_csv()                     |
+--------------------------------------------+------------------------------+-------------------------------------+
| tfrecord                                   | tf.TFRecordReader()          | tf.parse_single_example()           |
|                                            |                              | 将Example protocol buffer解析为张量 |
+--------------------------------------------+------------------------------+-------------------------------------+
| 固定长度的记录                             | tf.FixedLengthRecordReader() | tf.decode_raw()                     |
| e.g. the CIFAR-10 dataset,                 |                              |                                     |
| 每条记录的长度都是固定的，                 |                              |                                     |
| 一个字节的标签，后面是3072字节的图像数据。 |                              |                                     |
+--------------------------------------------+------------------------------+-------------------------------------+

Pipe and Queue
----------------
在本节的内容中，从文件中读取数据时，tf的实现方式就是基于queue的，有必要了解一些python实现pipe和queue的基本知识。在2016年实现的亚太主要英文媒体的舆情监控系统中其实已经用到了multiprocessing.Queue。

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

Threading and Queues in TF
-----------------------------
Attention
^^^^^^^^^^^^
`on api_guides <https://www.tensorflow.org/api_guides/python/threading_and_queues#Queue_usage_overview>`_

In versions of TensorFlow before 1.2, we recommended using multi-threaded, queue-based input pipelines for performance. Beginning with TensorFlow 1.4, however, we recommend using the tf.data module instead. The tf.data module offers **an easier-to-use interface for constructing efficient input pipelines**. Furthermore, we've stopped developing the old multi-threaded, queue-based input pipelines.