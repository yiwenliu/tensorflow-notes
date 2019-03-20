安装NVIDIA驱动
=====================
检查是否安装成功
-------------------
甭管系统中是否自带驱动，运行下列命令，如果不能正常显示，那么就得重新安装显卡驱动。

$nvidia-smi #可显示显卡一些信息，显卡的个数等

$nvidia-settings #可以打开显卡设置的图像界面(NVIDIA X Server Settings)

另外还有命令：

.. code-block:: none
    :linenos:

    #显示系统中所有PCI总线设备或连接到该总线上的所有设备的工具
    $lspci | grep VGA 或 lspci | grep NVIDIA
    #已载入系统的模块
    $lsmod | grep nvidia
    $glxinfo | grep render
    #输出：OpenGL renderer string: GeForce GTX 1080 Ti/PCIe/SSE2

on ubuntu16
-----------------
从官网下载驱动文件.run来安装驱动。

1. 查看ubuntu的默认驱动

.. code-block:: none
    :linenos:

    $lspci -v
    OR
    $glxinfo | grep render #从输出中的两行，可以解析四种"rendering/OpenGL/X display"的工作方式

- 直接渲染，可以简单理解为本地渲染，即在同一台机器上的 X Client/Server 环境中实现三维图形渲染；
- 相应地，间接渲染可以理解为远程渲染，也就是在网络环境中实现三维图形硬件加速渲染。

+--------------------+--------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------+
|                    | direct rendering                                                                     | indirect rendering                                                                   |
|                    |                                                                                      |                                                                                      |
|                    | ( all 3D rendering commands are handled by the X client application                  | ( all rendering commands are sent to the X server)                                   |
|                    |                                                                                      |                                                                                      |
|                    |                                                                                      |                                                                                      |
|                    | ,so that the X server is free to service requests from other applications.)          |                                                                                      |
+--------------------+--------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------+
| software rendering | direct rendering: Yes                                                                |                                                                                      |
|                    |                                                                                      |                                                                                      |
|                    | OpenGL renderer string: Software Rasterizer                                          |                                                                                      |
+--------------------+--------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------+
| hardware rendering | direct rendering: Yes                                                                | direct rendering: No (LIBGL_ALWAYS_INDIRECT set)                                     |
|                    |                                                                                      |                                                                                      |
|                    | OpenGL renderer string: Mesa DRI Intel(R) 945GM GEM 20090326 2009Q1 RC2 x86/MMX/SSE2 | OpenGL renderer string: Mesa DRI Intel(R) 945GM GEM 20090326 2009Q1 RC2 x86/MMX/SSE2 |
+--------------------+--------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------+

在我的新装机器中，$glxinfo | grep render输出中的对应行如下,表明使用的是“开源驱动”：

OpenGL rendering string: Gallium 0.4 on llvmpipe (LLVM 4.0, 256 bits)

2. 获取驱动

去官网获取驱动，放在home目录下。

- 产品类型：geforece
- 产品系列：geforce 10 series
- 产品家族：geforce gtx 1080 ti

3. 卸载ubuntu系统中自带的nvidia驱动版本

$sudo apt-get remove --purge nvidia*

4. 编译依赖

执行$uname -r，比如我的结果是4.4.0-98-generic

执行
$sudo apt-get install build-essential pkg-config xserver-xorg-dev linux-headers-4.4.0-98-generic

5. 屏蔽开源驱动nouveau

安装过程会询问是否屏蔽，手动屏蔽也有多种操作方式，如通过blacklist.conf或Grub2。这里选择blacklist：

创建新文件：
$sudo vim /etc/modprobe.d/blacklist-nouveau.conf

内容为

blacklist nouveau

options nouveau modeset=0

再更新$sudo update-initramfs  -u

修改后需要重启系统。

确认下Nouveau是已经被你干掉，使用命令： $lsmod | grep nouveau

6. 结束X-window服务

  安装Nvidia驱动需要结束x-window服务，据说这一步很重要。

按Ctrl + Alt + F1 进入tty1控制台，输入用户名密码登录，然后键入命令后执行（数字不能用小键盘输入）：

$sudo service lightdm stop

7. 安装驱动

（1）进入blos关闭secure boot （华硕还有fast boot）
Make sure the "OS Type" is "Windows UEFI"

Enter "Key Management"

Select "Clear Secure Boot keys" (You will have the option "Install default Secure Boot keys" to restore the default keys after you cleared the Secure Boot Keys)

After you cleared the Secure Boot Keys, Secure Boot will be automatically disabled. You can set the OS Type to Other OS now.

（2）执行驱动安装的run文件
$sudo sh ./NVIDIA*.run --no-opengl-files
（如果安装不正常则带 --uninstall 参数卸载）

- 报错The distribution-provided pre-install script failed!不必理会，继续安装；
- 最重要的一步，安装程序问你是否使用nv的xconfig文件，nvidia-xconfig, 这里一点要选yes，否则在启动x-window时不会使用nv驱动。
- –-no-opengl-files：表示只安装驱动文件，不安装OpenGL文件。这个参数不可省略，否则会导致登陆界面死循环，英语一般称为”login loop”或者”stuck in login”。因为NVIDIA的驱动默认会安装OpenGL，而Ubuntu的内核本身也有OpenGL、且与GUI显示息息相关，一旦NVIDIA的驱动覆写了OpenGL，在GUI需要动态链接OpenGL库的时候就引起问题。

8. 重启X 服务

$sudo service lightdm start
（然后Ctrl+Alt+F7进入图形界面，在我的系统中，这一步没有出现）

9. 卸载驱动

sudo sh ~/NVIDIA-Linux-x86_64-367.44.run --uninstall

系统会提示，是否使用nvidia-xconfig --restore-original-backup来恢复以前的x configuration file？
选择Yes

10. 检查

nvidia-smi 可显示显卡一些信息

nvidia-settings 显卡设置

另外还有命令：

.. code-block:: none
    :linenos:

    #显示系统中所有PCI总线设备或连接到该总线上的所有设备的工具
    $lspci | grep VGA 或 lspci | grep NVIDIA
    #已载入系统的模块
    $lsmod | grep nvidia
    $glxinfo | grep render
    #输出：OpenGL renderer string: GeForce GTX 1080 Ti/PCIe/SSE2

11. 安装驱动成功后，界面的分辨率更高了，字号更小了。

on ubuntu18
-----------------
方法1：用图形界面安装gtx1060驱动(recommended)

`Reference Link <https://blog.csdn.net/qq_28660035/article/details/78702535>`_

方法2：命令行方式
这个方法来自于 `How to install the NVIDIA drivers on Ubuntu 18.04 Bionic Beaver Linux <https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-18-04-bionic-beaver-linux>`_

First, detect the model of your nvidia graphic card and the recommended driver. To do so execute:

.. code-block:: none
    :linenos:

    $ubuntu-drivers devices
    == /sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0 ==
    modalias : pci:v000010DEd00001180sv00001458sd0000353Cbc03sc00i00
    vendor   : NVIDIA Corporation
    model    : GK104 [GeForce GTX 680]
    driver   : nvidia-304 - distro non-free
    driver   : nvidia-340 - distro non-free
    driver   : nvidia-384 - distro non-free recommended
    driver   : xserver-xorg-video-nouveau - distro free builtin

    == cpu-microcode.py ==
    driver   : intel-microcode - distro free

From the above output we can conclude that the current system has NVIDIA GeForce GTX 680 graphic card installed and the recommend driver to install is nvidia-384. If you agree with the recommendation feel free to use ubuntu-drivers command again to install all recommended drivers:

.. code-block:: none
    :linenos:

    $ sudo apt-get  install nvidia-384

Once the installation is concluded, reboot your system and you are done.

卸载nv驱动
---------------
每种安装方式都有对应的卸载方式

1. 用图形界面卸载
2. $sudo apt-get remove --purge nvidia-*
3. $sudo sh ~/NVIDIA-Linux-x86_64-367.44.run –uninstall

- 系统会提示，是否使用nvidia-xconfig –restore-original-backup来恢复以前的x configuration file？ 选择Yes

用Intel集成显卡连接显示器
--------------------------
在6张gtx1060的机器上，安装完nv驱动后，依然使用intel集成显卡输出视频信号。

1.  system settings > details, 显示ubuntu正在使用的intel集成显卡
2. 但是，使用nvidia-settings打开"nvidia x server setting" > prime profiles时，显示使用的是NVIDIA 