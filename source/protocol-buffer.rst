Protocol Buffers
===================
All of TensorFlow's file formats, including TFRecord-formatted files, are based on Protocol Buffers. We often refer to Protocol Buffers as protobufs. 

Basics tutorial
-----------------
`Protocol Buffer Basics: Python 
<https://developers.google.com/protocol-buffers/docs/pythontutorial>`_

How to use
-------------
1. 写.proto文件

- 写.proto文件类似于定义struct或者定义class。但是，思考如果用python普通的定义class的方法来实现 `Basics-tutorial <https://developers.google.com/protocol-buffers/docs/pythontutorial>`_ 中给出的例子，会有多麻烦。因为要体现类的继承、包含和引用的关系。
- 定义message时，只定义了属性，并未定义方法。

2. 编译，generate the classes, a .py file

-编译后，定义的message成为了class
-编译后，给编译得到的class添加了一些方法，e.g. ParseFromString()和SerializeToString()，这两个方法的典型用法见 `Writing A Message <https://developers.google.com/protocol-buffers/docs/pythontutorial#writing-a-message>`_

3. 使用.py中的class

`Writing A Message <https://developers.google.com/protocol-buffers/docs/pythontutorial#writing-a-message>`_


(反)序列化
------------
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

.proto file format
----------------------

.. code-block:: none

  #start with a package declaration
  package xxx;  

  # a message definition
  message message-type-name {
    #define a field in the message
    modifier field-type field-name = tag;
  }

  #define enum type
  enum type-name {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

.proto v.s. .py
------------------
下述链接有编译后的.py文件，可以比较事先定义的.proto文件
https://developers.google.com/protocol-buffers/docs/pythontutorial#the-protocol-buffer-api