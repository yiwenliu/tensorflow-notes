SVM
=======
SVM vs Logistic Regression vs Neural Network
-------------------------------------------------
1. 3种方法都可用于“监督学习”中的“分类问题”，根据"number of features"和"numbers of training examples"的相对关系做出选择，详见《ML 笔记》 week7 p63

2. SVM和LR更像，在一些方面作了简化，在另外的方面进行了强化：

- 在cost function和hypothesis上做出了简化；
- 在对features处理上进行了强化（主要目的是为了适应上述2个方面的简化）。

3. 和另外2个机器学习方法一样，SVM也会存在overfitting/underfitting的问题，只是影响因素不同了。

features
-----------
+----------------+-----------------------+----------------------------+
|                | logistic regression   | SVM                        |
+----------------+-----------------------+----------------------------+
| 不扩充features |                       | linear kernel              |
+----------------+-----------------------+----------------------------+
| 扩充features   | high order polynomial | kernel/similarity function |
+----------------+-----------------------+----------------------------+

Kernels
^^^^^^^^^^^
是否需要使用“kernels”或者称为“similarity functions”

hypothesis
-------------
1. LR的hypothesis如下图所示

.. image:: img/lr-hypothesis.png

2. SVM的hypothesis如下图所示

.. image:: img/svm-hypothesis.png

- svm的hypothesis不像LR输出的是probability，而是make a prediction of y=1 or 0 directly.
- 公式中的X即可能是original kernel，又可能是经过kernel function扩维后的new features.

cost function
----------------

.. image:: img/cost_lr_svm.png

- SVM对LR的cost function进行了简化
- 公式中的X即可能是original kernel，又可能是经过kernel function扩维后的new features.