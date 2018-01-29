TFRecord-formatted files
=============================

使用场景
----------
准备MTCNN训练数据时，把图像数据和annotations一起写入一个tfrecord格式文件中。

A TFRecords file represents a sequence of (binary) strings. The format is not random access, so it is suitable for streaming large amounts of data but not suitable if fast sharding or other non-sequential access is desired.

处理模块
---------
`tf.python_io module 
<https://www.tensorflow.org/api_docs/python/tf/python_io>`_ including python functions for directly manipulating TFRecord-formatted files.

tfrecord格式文件的组成
-----------------------
`A TFRecords file
<https://www.tensorflow.org/api_guides/python/python_io#tfrecords_format_details>`_ contains a sequence of strings with CRC hashes. Each record has the following format and the records are concatenated together(串在一起) to produce the file. 

.. code-block:: python
  :linenos:

  uint64 length
  uint32 masked_crc32_of_length
  byte   data[length]
  uint32 masked_crc32_of_data

上述record format中 **byte   data[length]** 是真正的payload，它不是简单的string，而是采用protocol buffer定义的数据结构序列化后的string

How to write a TFRecords file
-------------------------------

1. 初始化一个文件句柄 `TFRecordWriter <https://www.tensorflow.org/api_docs/python/tf/python_io/TFRecordWriter>`_
2. 构建a record, a **tf.train.Example object**
3. TFRecordWriter.write(record.SerializeToString())
4. 重复2-3
5. 关闭TFRecordWriter

构建record
^^^^^^^^^^^
record并非简单的一个dict，而是一个 :ref:`tf.train.Example Object <example-proto>`

class Example的结构定义在 :ref:`example.proto <example-proto>` 
和 :ref:`feature.proto <feature-proto>` 中

How to read a TFRecords file
-------------------------------