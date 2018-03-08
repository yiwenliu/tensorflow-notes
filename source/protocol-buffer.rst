Protocol Buffers
===================
All of TensorFlow's file formats, including TFRecord-formatted files, are based on Protocol Buffers. We often refer to Protocol Buffers as protobufs. 

Reference
----------
Google提供了一个官方的帮助页面（本小节的内容多出自于此处）， `Protocol Buffer Basics: Python
<https://developers.google.com/protocol-buffers/docs/pythontutorial>`_

What to
--------
Serialize and retrieve structured data.

(反)序列化
^^^^^^^^^^^
- 序列化： 将**数据结构或对象**转换成 **二进制串** 的过程

在python中常用的dict对象串行化（序列化）模块是**json**

.. code-block:: python
  :linenos:

  import json
  data = {
    'name' : 'ACME',
    'shares' : 100,
    'price' : 542.23
  }
  json_str = json.dumps(data) #type(json_str) is str

- 反序列化：将在序列化过程中所生成的**二进制串**转换成**数据结构或者对象**的过程

可以新建一个文件**data.json**，其内容就是{'name' : 'ACME','shares' : 100,'price' : 542.23}, 执行下面的代码就能把这个.json文件读入内存，成为一个python dict对象

.. code-block:: python
  :linenos:

  # Reading data back
  with open('data.json', 'r') as f:
    data = json.load(f)

Why Protocl Buffer 
---------------------
在 `Google Protocol Buffer Basics <https://developers.google.com/protocol-buffers/docs/pythontutorial#why-use-protocol-buffers>`_ 中，给出了三种（反）序列化的方法，分别描述了各自的不足，并给出了要创造protocol buffer的原因， the protocol buffer compiler解决了所有的不足。

为什么要用.proto，而不直接在.py中定义class？但是，思考如果用python普通的定义class的方法来实现 `Basics-tutorial <https://developers.google.com/protocol-buffers/docs/pythontutorial>`_ 中给出的例子，会有多麻烦，因为要体现类的继承、包含和引用的关系，还要定义相应的方法。

下述链接有编译后的.py文件，可以比较事先定义的.proto文件
https://developers.google.com/protocol-buffers/docs/pythontutorial#the-protocol-buffer-api

How to use
-------------
1. 写.proto文件
^^^^^^^^^^^^^^^^^^

- add a **message** for each data structure you want to serialize, then specify a name and a type for each field in the message, 类似于定义struct或者定义class。

.. code-block:: none

  #start with a package declaration which helps to prevent naming conflicts between #different projects
  package xxx;  

  # a message definition
  message message-type-name {
    #define a field in the message
    #modifier: required/optional/repeated
    #field-type: 1)simple data types including bool, int32, float, double, and string;
    #            2)other message types as field types
    #tag: the unique "tag" that field uses in the binary encoding
    modifier field-type field-name = tag;
  }

You'll find a complete guide to writing .proto files – including all the possible field types – in the `Protocol Buffer Language Guide <https://developers.google.com/protocol-buffers/docs/proto>`_.

- 定义message时，只定义了属性，并未定义方法。

2. 编译
^^^^^^^^

generate the classes, a .py file

- 编译后，定义的message成为了class
- 编译后，并没有直接给编译得到的class添加方法，e.g. 常用的ParseFromString()和SerializeToString()，这两个方法的典型用法见 `Writing A Message <https://developers.google.com/protocol-buffers/docs/pythontutorial#writing-a-message>`_ 。而是使用了metaclass，见 `The Protocol Buffer API <https://developers.google.com/protocol-buffers/docs/pythontutorial#the-protocol-buffer-api>`_ 中的讲解

3. 使用编译后的.py中的class
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`Writing A Message <https://developers.google.com/protocol-buffers/docs/pythontutorial#writing-a-message>`_

4. 实例化
^^^^^^^^^^^
用数据填充好的class，序列化后，可以保存为一个文本文件（不同于.proto的定义文件）。

5. Reading a Message
^^^^^^^^^^^^^^^^^^^^^^
目前碰到了两种途径：

- 用编译后的class所自带的类方法ParseFromString(data), `Reading A Message <https://developers.google.com/protocol-buffers/docs/pythontutorial#reading-a-message>`_

.. code-block:: python
  :linenos:

  address_book = addressbook_pb2.AddressBook()

  # Read the existing address book.
  f = open(sys.argv[1], "rb")
  address_book.ParseFromString(f.read())
  f.close()

- 如果是从tfrecord文件中读取，可以使用tf.parse_single_example(), `Read the TFRecords file <http://www.machinelearninguru.com/deep_learning/tensorflow/basics/tfrecord/tfrecord.html>`_

.. code-block:: python
  :linenos:

  #Define a decoder
  #@return: A dict mapping feature keys to Tensor and SparseTensor values.
  image_features = tf.parse_single_example(
      serialized_example,
      features={
          'image/encoded': tf.FixedLenFeature([], tf.string),#one image  one record
          'image/label': tf.FixedLenFeature([], tf.int64),
          'image/roi': tf.FixedLenFeature([4], tf.float32),
          'image/landmark': tf.FixedLenFeature([10],tf.float32)
      }
  )

.. _example-proto:

TF中的应用
------------
General
^^^^^^^^^
All of TensorFlow's file formats which store data on disk are based on Protocol Buffers, including:

- tfrecord file
- Event files, involving summaries and events to analyze and visualize TensorFlow
- saving a Graph object
- Model files

example.proto
^^^^^^^^^^^^^^^^
Usage&Background
+++++++++++++++++++++
训练MTCNN时，要构建tfrecord文件，这个文件中每一条record都是序列化后的Example Object

Definition Of This File
+++++++++++++++++++++++++++
https://github.com/tensorflow/tensorflow/blob/r1.5/tensorflow/core/example/example.proto

.. code-block:: python
  :linenos:

  message Example {
    Features features = 1;
  };

  message Features {
    // Map from feature name to feature.
    map<string, Feature> feature = 1;
  };

  message Feature {
  // Each feature can be exactly one kind.
    oneof kind {
      BytesList bytes_list = 1;
      FloatList float_list = 2;
      Int64List int64_list = 3;
    }
  };

  // Containers to hold repeated fundamental values.
  message BytesList {
    repeated bytes value = 1;
  }
  message FloatList {
    repeated float value = 1 [packed = true];
  }
  message Int64List {
    repeated int64 value = 1 [packed = true];
  }

这个proto文件对应的类
++++++++++++++++++++++
这个proto文件定义了两个message type，于是又两个class与之对应

- class `tf.train.Example <https://www.tensorflow.org/api_docs/python/tf/train/Example>`_
- class `tf.train.SequenceExample <https://www.tensorflow.org/api_docs/python/tf/train/SequenceExample>`_

How To Use
+++++++++++++++
.. code-block:: python
  :linenos:

  example = tf.train.Example(features=tf.train.Features(feature={
        'image/encoded': _bytes_feature(image_buffer),
        'image/label': _int64_feature(class_label),
        'image/roi': _float_feature(roi), #ROI: region of interest
        'image/landmark': _float_feature(landmark)
  }))

上述代码中，tf.train.Example()应该等同于example.Example()

.. _feature-proto:

feature.proto
^^^^^^^^^^^^^^^
Definition
++++++++++++
https://github.com/tensorflow/tensorflow/blob/r1.5/tensorflow/core/example/feature.proto

event.proto
^^^^^^^^^^^^^^
Protocol buffer representing an event that happened during the execution of a Brain model.

在tensorflow visualization中使用到。

event.proto中定义的message“包含”了summary.proto中定义的message。

summary.proto
^^^^^^^^^^^^^^^