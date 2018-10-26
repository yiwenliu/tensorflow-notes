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
| 不扩充features |         -----         | linear kernel              |
+----------------+-----------------------+----------------------------+
| 扩充features   | high order polynomial | kernel/similarity function |
+----------------+-----------------------+----------------------------+