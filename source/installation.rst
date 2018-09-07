Installing
==============

安装python环境
----------------------
ubuntu中自带python，而还需要安装anaconda的原因是，在安装tensorflow时，建议最好使用anaconda。

安装方法：《tensorflow实战》3.1.1

安装路径：/home/superben/anaconda3

当在cmd中运行python时，会寻找到anaconda的python，因为/home/anaconda/bin添加到了PATH的最前面，shell在找到anaconda的python后，就不会继续寻找unbuntu系统自带的python了。

安装cuDNN
----------------------------
安装过程见《tf实战》p44，在书中的下载页面中登录后，作出如下图的选择，因为

- 如此，下载的就是.tgz格式而非.dep格式的文件
- import tensorflow就不会报错“找不到libcudnn.so.6”

.. image:: img/cudnn-1.png

其实，安装cuDNN就是把三个libcudnn.so.*文件放入CUDA的安装路径的库文件夹/usr/local/cuda/lib64下


安装tensorflow
----------------------------
请参考官方文档 `Installing TensorFlow on Ubuntu <https://www.tensorflow.org/install/install_linux>`_ 下"Installing with Anaconda"

windows anaconda环境下安装tf
----------------------------

官方文档 `install tf on windows with anaconda <https://www.tensorflow.org/install/install_windows#installing_with_anaconda>`_

注意：

- 使用anaconda prompt来执行上述步骤中的命令，而不是windows自带的cmd
- 在第2步，创建tf环境时python的版本一定要选3.5，anaconda会在Anaconda3\\envs\\tensorflow下重新装一个3.5版本的python
- 在anaconda的虚拟环境中安装tf，也要用pip install，而不是conda install
- tf的安装路径，Anaconda3\\envs\\tensorflow\\Lib\\site-packages\\tensorflow

Use tensorflow under Windows&Anaconda
---------------------------------------
tensorflow的一些运行结果需要快速验证，e.g.对于tensor的变换操作。

1. Open Anaconda Prompt
2. Run 'activate tensorflow'
3. Run 'pyhon' to Invoke python
4. Enter programs

配置spyder在tensorflow的虚拟环境中工作
---------------------------------------

1. 在多版本anaconda python环境下转换spyder, https://www.zhihu.com/question/49144687
2. 配置python interpreter

.. image:: img/install-tf-1.jpg

MTCNN在PC/GPU-machine中的路径
-------------------------------
- PC

D:\spyder_project\github\mtcnn\MTCNN-Tensorflow

- GPU-machine

~/tf_action/MTCNN-Tensorflow