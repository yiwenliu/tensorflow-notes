FCN
=====

network structure
-------------------
2015年，在CVPR会议上提出FCN。

FCN把CNN中最后的“全连接层”全部转化为1*1的卷积核的卷积层。

CNN 的输入是图像，输出是一个一维向量，表示此输入图像属于每个分类的概率值。

FCN的输入是一张图片（H*W*3），输出也是一张图片（H*W），表示每一个像素？

FCN 使用的识别库是 PASCAL VOC，在 PASCAL VOC 中有 20 种物体分类，另外一个 background 分类，一个分类对应一种颜色，一共有21种颜色。

.. image:: img/fcn-1.jpg