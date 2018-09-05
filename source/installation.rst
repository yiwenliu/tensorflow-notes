Installing
==============

安装python环境
----------------------
ubuntu中自带python，而还需要安装anaconda的原因是，在安装tensorflow时，建议最好使用anaconda。

安装方法：《tensorflow实战》3.1.1

安装路径：/home/superben/anaconda3

当在cmd中运行python时，会寻找到anaconda的python，因为/home/anaconda/bin添加到了PATH的最前面，shell在找到anaconda的python后，就不会继续寻找unbuntu系统自带的python了。

安装CUDA
----------------------------

在下述的ubuntu的两个版本中，在cuda的下载页面都是选择.run安装文件，

on ubuntu16
^^^^^^^^^^^^^^^^^
CUDA安装包里集成了显卡驱动，下载时请选择cuda8.0，否则在import tensorflow时，会报错“找不到*.so.8”。

安装路径/usr/local/cuda-8.0

安装过程见《tf实战》p43

on ubuntu18
^^^^^^^^^^^^^^^^

.. code-block:: none
    :linenos:

	# CUDA 9 requires gcc 6
	sudo apt install gcc-6
	sudo apt install g++-6

	# downoad one of the "runfile (local)" installation packages from cuda toolkit archive 
	wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run

	# make the download file executable
	chmod +x cuda_9.0.176_384.81_linux-run 
	sudo ./cuda_9.0.176_384.81_linux-run --override

	# answer following questions while installation begin
	# You are attempting to install on an unsupported configuration. Do you wish to continue? y
	# Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81? n
	# Install the CUDA 9.0 Toolkit? y
	# 建立了符号链接 /usr/local/cuda -> /usr/local/cuda-9.2

	# set up symlinks for gcc/g++
	sudo ln -s /usr/bin/gcc-6 /usr/local/cuda/bin/gcc
	sudo ln -s /usr/bin/g++-6 /usr/local/cuda/bin/g++

	# setup your paths
	echo 'export PATH=/usr/local/cuda-9.0/bin:$PATH' >> ~/.bashrc
	echo 'export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
	source ~/.bashrc

卸载
^^^^^^^^
run the uninstall scripte in /usr/local/cuda-9.2/bin

安装cuda中包含的nv驱动
^^^^^^^^^^^^^^^^^^^^^^^^
在cuda的安装过程中，没有安装其中包括的nv驱动，

sudo <cudaInstaller>.run -silent -driver

设置CUDA的路径
^^^^^^^^^^^^^^^^^^^
on ubuntu16, 过程见《tf实战》p44

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