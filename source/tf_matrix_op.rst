TF中的矩阵Operation
=====================
矩阵相乘
---------
.. code-block:: python
  :linenos:

  tf.matmul(h_pool, W)

用于全连接的神经网络, e.g.CNN中的全连接层

矩阵按元素相乘
---------------
.. code-block:: none
  :linenos:

  matrix_1 * matrix_2

矩阵按行/列求和
----------------
.. code-block:: python
  :linenos:

  tf.reduce_sum(matrix)