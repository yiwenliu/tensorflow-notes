Installing
==============

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