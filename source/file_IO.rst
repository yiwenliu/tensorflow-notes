TF file API
=============
处理模块
-------------
The main roles of the **tf.gfile** module are:

- To provide an API that is close to Python's file objects, and
- To provide an implementation based on TensorFlow's C++ FileSystem API.

The C++ FileSystem API supports multiple file system implementations, including local files, Google Cloud Storage (using a gs:// prefix), and HDFS (using an hdfs:// prefix). TensorFlow exports these as  tf.gfile, so that you can use these implementations for saving and loading checkpoints, writing TensorBoard logs, and accessing training data (among other uses). However, if all of your files are local, you can use the regular Python file API without any problem.

查看文件是否存在
-----------------
tf.gfile.Exists()

TFRecord-formatted files
=============================

使用场景
----------
准备MTCNN训练数据时，把图像数据和annotations一起写入一个tfrecord格式文件中。

A TFRecords file represents a sequence of (binary) strings. The format is not random access, so it is suitable for streaming large amounts of data but not suitable if fast sharding or other non-sequential access is desired.

处理模块
---------
tf.python_io module including python functions for directly manipulating TFRecord-formatted files.

tfrecord格式文件的组成
-----------------------
A TFRecords file contains a sequence of strings with CRC hashes. Each record has the following format and the records are concatenated together(串在一起) to produce the file. 

.. code-block:: python
  :linenos:

  uint64 length
  uint32 masked_crc32_of_length
  byte   data[length]
  uint32 masked_crc32_of_data

How to write a TFRecords file
-------------------------------

1. 初始化一个TFRecordWriter
2. 构建a record, a **tf.train.Example object**
3. TFRecordWriter.write(record.SerializeToString())
4. 重复2-3
5. 关闭TFRecordWriter

构建record
^^^^^^^^^^^
record并非简单的一个dict，而是一个**tf.train.Example Object**

How to read a TFRecords file
-------------------------------