安装CUDA
==============
版本
----------------
tensorflow, cuda, cudnn的版本必须要对应，否则在import tensoflow时，会报找不到.so的错误

CUDA 8.0→cuDNN v5.1 / CUDA 8.0→cuDNN v6.0 / CUDA 9.0→cuDNN v7.0.5

另外，tensorflow 1.6/1.5和CUDA ９.0对应，1.4/1.3和CUDA 8.0对应

cuda安装包
----------------
1. 在下述的ubuntu的两个版本中，在cuda的下载页面都是选择.run安装文件
2. 安装路径/usr/local/cuda-x.x

安装版本匹配的gcc
--------------------
ubuntu中的gcc版本往往过高，ubuntu16是gcc6， ubuntu18是gcc7

1. cuda8使用 gcc-5.4

- 使用apt-get时，显示找不到5.4版本的gcc，于是在google上下载了一个deb的包，gcc54-c++5.4.0-ubuntu16_amd64.deb, 在桌面用双击deb包来安装的。
- 因为os中往往会安装多个版本的gcc，所以符号链接还是需要建立的，代码如下一点。

2. cuda9使用 gcc-6

.. code-block:: none
    :linenos:

    $sudo apt-get install gcc-x
    $sudo apt-get install g++-x

    #gcc安装成功会在/usr/bin下有可执行文件
    $cd /usr/bin
    #如果以前建立了符号链接，删除之
    $sudo unlink gcc
    $sudo unlink g++
    #建立符号链接，这样，在安装cuda时，就会判断gcc是与之匹配了
    $sudo ln -s gcc-x gcc
    $sudo ln -s g++-x g++

install on ubuntu16
--------------------------
CUDA安装包里集成了显卡驱动，下载时请选择cuda8.0，否则在import tensorflow时，会报错“找不到*.so.8”。

安装路径/usr/local/cuda-8.0

安装过程见《tf实战》p43

install on ubuntu18
-------------------------

.. code-block:: none
    :linenos:

    #这几步在ubuntu16上不需要，因为在ubuntu18上，会导致./cuda8-run运行失败，在log文件中显示“can't locate InstallUtils.pm in @INC”
    $cd ~/Downloads
    $ ./cuda*.run --tar mxvf # unpack .run file under ~/Downloads
    $sudo cp  InstallUtils.pm /usr/lib/x86_64-linux-gnu/perl-base
    $ export $PERL5LIB 


    # downoad one of the "runfile (local)" installation packages from cuda toolkit archive 
    $wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run

    # make the download file executable
    #不要使用--override，其意义是不做gcc的版本检查
    $chmod +x cuda_*_linux-run 
    $sudo ./cuda_*_linux-run

    # answer following questions while installation begin
    # You are attempting to install on an unsupported configuration. Do you wish to continue? y
    # Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81? n
    # Install the CUDA 9.0 Toolkit? y
    # 建立了符号链接 /usr/local/cuda -> /usr/local/cuda-x.x

    *********./cuda_*_linux-run运行结束****************

    # setup your paths
    $echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
    $echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
    $echo 'export CUDA_HOME=/usr/local/cuda' >> ~/.bashrc #这个环境变量一定要设置
    $source ~/.bashrc

卸载
----------------
run the uninstall scripte in /usr/local/cuda-9.2/bin

安装cuda中包含的nv驱动
--------------------------
在cuda的安装过程中，没有安装其中包括的nv驱动，

sudo <cudaInstaller>.run -silent -driver

设置CUDA的路径
--------------------
on ubuntu16, 过程见《tf实战》p44