Session
=========

create a session
-----------------
Session的一头是Graph(client program)，一头是C++ runtime。

.. code-block:: python
  :linenos:

  # Create a default in-process session.
  with tf.Session() as sess:
  # ...

  # Create a remote session.
  with tf.Session("grpc://example.org:2222"):
  # ...

  g_1 = tf.Graph()
  with g_1.as_default():
  # Sessions created in this scope will run operations from `g_1`.
    sess_1 = tf.Session()

.. _session-config:

配置session参数
------------------
在初始化session时，设置__init__()中的config参数——A ConfigProto protocol buffer with configuration options for the session.

.. code-block:: python
  :linenos:

  #截取自mtcnn
  sess = tf.Session(config=tf.ConfigProto(allow_soft_placement=True, gpu_options=tf.GPUOptions(allow_growth=True)))

config参数既然是一个protocol buffer object，肯定有一个probuffer文件与之对应, `config.proto <https://github.com/tensorflow/tensorflow/blob/r1.6/tensorflow/core/protobuf/config.proto>`_ , 在代码中使用时，就是初始化一个Class ConfigProto。

Using tf.Session.run()
-----------------------

