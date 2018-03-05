TFRecord-formatted files
=============================
Background
------------
准备MTCNN训练数据时，把图像数据和annotations一起写入一个 **.tfrecord** 文件中。

适用场景
----------
A TFRecords file represents a sequence of (binary) strings. The format is not random access, so it is suitable for streaming large amounts of data but not suitable if fast sharding or other non-sequential access is desired.

Why Use
--------
在MTCNN中，为什么要把成千张的images和annotations放在一个.tfrecord文件中：

- easy to use

Binary files are sometimes easier to use, because you don’t have to specify different directories for images and groundtruth annotations. While storing your data in binary file, you have your data in one block of memory, compared to storing each image and annotation separately.

- time saving

Openning a file is a considerably time-consuming operation especially if you use hdd and not ssd, because it involves moving the disk reader head and that takes quite some time. Overall, by using binary files you make it easier to distribute and make the data better aligned for efficient reading.

Module&File API
-----------------
`tf.python_io module 
<https://www.tensorflow.org/api_docs/python/tf/python_io>`_ including python functions for directly manipulating TFRecord-formatted files.

tfrecord格式文件的组成
-----------------------
A TFRecords file, `tutorial
<https://www.tensorflow.org/api_guides/python/python_io#tfrecords_format_details>`_ , contains a sequence of strings with CRC hashes. **Each record** has the following format and the records are concatenated together(串在一起) to produce the file. 

.. code-block:: python
  :linenos:

  uint64 length
  uint32 masked_crc32_of_length
  byte   data[length]
  uint32 masked_crc32_of_data

Look inside 'byte data'
^^^^^^^^^^^^^^^^^^^^^^^^
上述tfrecord format中 **byte   data[length]** 就是一个record，真正的payload。

byte data是由结构化数据通过序列化而成，采用的是Google自家的 **Protocol Buffer**

How to write a TFRecords file
-------------------------------
Steps
^^^^^^
0. When training, It is very important to randomly shuffle images
1. 初始化一个文件句柄 `TFRecordWriter <https://www.tensorflow.org/api_docs/python/tf/python_io/TFRecordWriter>`_
2. 构建a record, a **tf.train.Example object**，就是tfrecord format file中的 **byte data**
3. TFRecordWriter.write(record.SerializeToString())
4. 重复2-3
5. 关闭TFRecordWriter

构建record
^^^^^^^^^^^
在MTCNN中，显然一个image和其对应的annotation组成了一个byte data。

构建record就是实例化一个 :ref:`tf.train.Example Object <example-proto>`，类似于dict的key-value对。 

class Example的结构定义在 :ref:`example.proto <example-proto>` 
和 :ref:`feature.proto <feature-proto>` 中

How to read a TFRecords file
-------------------------------

Image Transforming
---------------------
Steps
^^^^^^^
一个image要想存入tfrecord file，要经过如下几步：

.png/.jpg  ---> array ---> string ---> BytesList obj ---> Feature object

注意：during the first operation, the information about the dimensions of the image is lost and we have to use it to recover the original image in the second. So we will have to store the raw image representation along with the **dimensions of the original image**.

.. code-block:: python
	:linenos:

	%matplotlib inline

	import numpy as np
	import skimage.io as io

	cat_img = io.imread('cat.jpg')
	io.imshow(cat_img)

	# Let's convert the picture into string representation
	# using the ndarray.tostring() function 
	cat_string = cat_img.tostring()

	# Now let's convert the string back to the image
	# Important: the dtype should be specified
	# otherwise the reconstruction will be errorness
	# Reconstruction is 1d, so we need sizes of image
	# to fully reconstruct it.
	reconstructed_cat_1d = np.fromstring(cat_string, dtype=np.uint8)

	# Here we reshape the 1d representation
	# This is the why we need to store the sizes of image
	# along with its serialized representation.
	reconstructed_cat_img = reconstructed_cat_1d.reshape(cat_img.shape)

	# Let's check if we got everything right and compare
	# reconstructed array to the original one.
	np.allclose(cat_img, reconstructed_cat_img)

图像处理模块
^^^^^^^^^^^^^
- PIL
- skimage