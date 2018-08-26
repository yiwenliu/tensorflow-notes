Protocol Buffers
===================

Protocol Buffers实际上是关于结构化数据（structured data）序列化和检索（serialize and retrieve）的整套解决方案，它包括了：

1. use the protocol buffer language to 数据结构的定义，.proto文件（类似于定义python class的.py文件）
2. compiler
3. 定义了一个实例序列化成二进制串的格式，就像.xml文件必须写成<xml></xml>。
4. 提供了实例序列化和反序列化的API

All of TensorFlow's file formats, including TFRecord-formatted files, are based on Protocol Buffers. We often refer to Protocol Buffers as protobufs. Protobufs is to Serialize and retrieve structured data. 

Reference
----------
Google提供了一个官方的帮助页面（本小节的内容多出自于此处）， `Protocol Buffer Basics: Python
<https://developers.google.com/protocol-buffers/docs/pythontutorial>`_

相关概念
------------

结构化数据
^^^^^^^^^^^^
例如，Each person in a address book has a name, an ID, an email address, and a contact phone number.

例如，MTCNN的训练数据集中，每一条数据包括图片本身，face bondary box四个点的坐标，face landmark五个点的坐标。

(反)序列化
^^^^^^^^^^^^
(反)序列化涉及的核心问题是二进制串的格式。

- 序列化： 将**数据结构或对象**转换成 **二进制串** 的过程，并不包括把这个二进制串写在硬盘上。

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
+------------+----------------------+---------------------------------------------------------------------+
|            | 在python中的数据结构 | 保存数据的方法                                                      |
+------------+----------------------+---------------------------------------------------------------------+
| 结构化数据 | 1. dict              | 1. 数据库，mysql, mangodb                                           |
|            | 2. class             | 2. 序列化后保存成文件，文件格式包括protobuf,json,xml,python pickle  |
+------------+----------------------+---------------------------------------------------------------------+

在 `Google Protocol Buffer Basics <https://developers.google.com/protocol-buffers/docs/pythontutorial#why-use-protocol-buffers>`_ 中，给出了结构化数据序列化成二进制串的3种格式（xml, python pickling&an ad-hoc way)，分别描述了各自的不足，并给出了要创造protocol buffer的原因， the protocol buffer compiler解决了所有的不足。

因为，the protocol buffer **compiler** creates a class with following features:

1. implements automatic encoding and parsing of the protocol buffer data with an efficient binary format. 
2. provides getters and setters for the fields that make up a protocol buffer and
3. takes care of the details of reading and writing the protocol buffer as a unit.
4. supports the idea of extending the format over time in such a way that the code can still read data encoded with the old format. 

message实例序列化后的二进制串格式
---------------------------------
`protocol buffers encoding <https://developers.google.com/protocol-buffers/docs/encoding>`_ ，和上一小节中提及的"an ad-hoc way"类似。

How to use
-------------
既然如此，google提供一个把class或者dict序列化为protobuf的module不就完了吗，但是google做的更多，甚至把定义class的.py文件替换为.proto，并且提供了complier（complier的好处见上一小节），极大简化了数据结构的定义过程——思考如果用python普通的定义class的方法来实现 `Basics-tutorial <https://developers.google.com/protocol-buffers/docs/pythontutorial>`_ 中给出的例子，会有多麻烦，因为要体现类的继承、包含和引用的关系，还要定义相应的方法。

下述链接有编译后的.py文件，可以比较事先定义的.proto文件
https://developers.google.com/protocol-buffers/docs/pythontutorial#the-protocol-buffer-api

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

field types有3种，`scalar types <https://developers.google.com/protocol-buffers/docs/proto#scalar>`_, `enumerations <https://developers.google.com/protocol-buffers/docs/proto#enum>`_ and other message type

- `enumeration <https://developers.google.com/protocol-buffers/docs/proto#enum>`_ 可以定义在message内部，也可以定义在message外部，定义语法如下。但是，还是有一些不明之处：“字符串”并没有用引号括起来；起作用的是“字符串”还是等号右边的数字？

.. code-block:: none

  enum enum-type-name {
    字符串1 = 0;
    字符串2 = 1;
    字符串3 = 3;
    ...
  }

tag就是field numbers,对于这个数字的大小以及意义，见 `Assigning Field Numbers <https://developers.google.com/protocol-buffers/docs/proto#assigning-field-numbers>`_

- 可以给optional field设定默认值，如果没有设定，系统会根据field type给一个默认值，详见 `Optional Fields And Default Values <https://developers.google.com/protocol-buffers/docs/proto#optional>`_

modifier就是Field Rules,3个取值的意义见 `Specifying Field Rules <https://developers.google.com/protocol-buffers/docs/proto#specifying-field-rules>`_ ,到底在实际实例化时的应用还要看例子。

- 定义message时，只定义了属性，并未定义方法。

2. 编译
^^^^^^^^
`What's Generated From Your .proto? <https://developers.google.com/protocol-buffers/docs/proto#whats-generated-from-your-proto>`_

- 由message编译而来的class，和自己写的class到底牛逼在哪里呢？从 `protocol buffer api <https://developers.google.com/protocol-buffers/docs/pythontutorial#the-protocol-buffer-api>`_ 给出的例子可以看出：1）If you were to try to assign a field that isn't defined in the .proto file, an AttributeError would be raised；2）If you assign a field to a value of the wrong type, a TypeError will be raised.
- 使用了两个python的特色技术—— **descriptor and metaclass** 来为class添加方法，这些方法称为 `The Protocol Buffer API <https://developers.google.com/protocol-buffers/docs/pythontutorial#the-protocol-buffer-api>`_

.. code-block:: none

  #有以下的message定义，addressbook.proto
  message Person {
    required string name = 1;
    required int32 id = 2;
  }
  message AddressBook {
    repeated Person people = 1;
  }

  #编译以后，向AddressBook中添加Person的API是怎样的呢？
  #并非先实例化一个Person，在把这个实例add如AddressBook.people
  address_book = addressbook_pb2.AddressBook()
  person = address_book.people.add()
  person.name = "ben"
  person.id = 1
  #遍历AddressBook中的people的API如下
  for person in address_book.people:
    ...

- a special **EnumDescriptor** class for Python that's used to create a set of symbolic constants（符号常量） with integer values in the runtime-generated class.

3. 使用编译后的.py中的class
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
这一小节中使用的方法均来自编译时为class加入的protocol buffer api。

`Writing a Message to a File <https://developers.google.com/protocol-buffers/docs/pythontutorial#writing-a-message>`_

4. Reading a Message
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

oneof
------
oneof既不是modifier，也是不是filed-type。它更像是多个optional的一种简写形式。
详见 `Oneof <https://developers.google.com/protocol-buffers/docs/proto#oneof>`_

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

checkpoint file when saving variable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
:ref:`The result when saving variables <checkpoint>`

config when initializing a session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
:ref:`配置session参数 <session-config>`