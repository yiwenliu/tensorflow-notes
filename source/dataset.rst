Data Set
=========
深度学习的发展和数据集的发展密不可分。某个算法的提出可能就是为了解决某个数据集的相关问题。

显然，使用了数据集的都是监督型机器学习和深度学习。

数据集的组成
-------------

- training data
- validation data
- test data

PASCAL VOC
------------

Intro
^^^^^^^
http://host.robots.ox.ac.uk/pascal/VOC/voc2012/

在这个数据集上有多个competition，数据集的annotation会随competition不同而不同。

数据集包含文件
^^^^^^^^^^^^^^^

下载的是一个tar文件，解压缩后，有5个文件夹

- JPEGImages ：17125个.jpg图片

.. image:: img/VOC-1.jpg

- Annotations ：17125个对应的.xml文件

xml文件包括图片的基本信息、图中所含object的name和bndbox等，以及该图片是否被segmented。

- SegmentationClass：2913个png文件

用于"segmentation competition" 
的class segmentation, 用颜色区分包括背景在内的21个class，相同class的不同object的颜色相同。

.. image:: img/VOC-2.png

- SegmentationObject：2913个png文件

用于"segmentation competition" 
的object segmentation,用颜色区分相同class的不同object

.. image:: img/VOC-3.png

- ImageSets

不同的competition有各自的子文件夹，其中的.txt文件写明了JPEGImages目录中的哪些图片文件用于不同的competition

1. train.txt: Training data
2. val.txt: Validation data (suggested). The validation data may be used as additional training data (see below).
3. trainval.txt: The union of train and val.
#. test.txt: Test data. The test set is not provided in the development kit. It will be released in good time before the deadline for submission of results.


Segmentation Competition & FCN
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
图像的语义分割是像素级别的分类问题。

:ref:`FCN <FCN-label>`
就是2015参加"Segmentation Competition"而提出的。

AFW
-----
Annotated Faces in the Wild

来自于一片论文《Face Detection, Pose Estimation, and Landmark Localization in the Wild》

虽然没有找到可以下载的页面，但是可以从论文pdf的图片中窥见一斑，联合了boundary box和landmark

.. image:: img/AFW-1.png

AFLW
-------
Home Page
^^^^^^^^^^^^
Annotated Facial Landmarks in the Wild(在野外有注释的面部标志)

https://www.tugraz.at/institute/icg/research/team-bischof/lrs/downloads/aflw/

需要发送email请求才能得到数据集

Features
^^^^^^^^^^^
In total about 25k faces in 21997 real-world images are annotated with up to 21 landmarks per image.

.. image:: img/AFLW-1.png

FDDB
------
Face Detection Data Set and Benchmark

Home Page
^^^^^^^^^^^
http://vis-www.cs.umass.edu/fddb/

WIDER FACE
-------------
Intro
^^^^^^^^^^^^
WIDER FACE dataset is a face detection benchmark dataset

http://mmlab.ie.cuhk.edu.hk/projects/WIDERFace/index.html

.. image:: img/WIDER-1.jpg

Annotation
^^^^^^^^^^^^
标注文件的每一行有两种可能的含义

image-path left-top-X left-top-Y right-bottom-X right-bottom-Y

image-path left-top-X left-top-Y width height