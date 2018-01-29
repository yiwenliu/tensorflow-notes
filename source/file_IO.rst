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