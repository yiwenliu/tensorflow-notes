Programming model
=====================

TF采用了Dataflow programming model，那么这个programming model到底是什么？

Intro
---------------------------------
虽然每一种编程语言都一个“execution model”，但是，依然存在独立于编程语言的“执行模型”，又可称为programming model, 它们对编程语言的execution model进行了扩展，包括indivisible unit of work, order in which units of work take place等等方面，例如，

- POSIX Threads library
- Hadoop's Map-Reduce programming model
- Tensorflow所采用的dataflow programming model

Programming model和a normal library的区别
--------------------------------------------
在写TF的应用程序时会调用TF API，在写法上和调用普通的python library的api没有什么区别，但是，在execute model的层面，却有天壤之别。

What distinguishes a programming model from a normal library is that **the behavior of the call cannot be understood in terms of the language the program is written in**. The reason is that the call invokes an execution model that is different from the execution model of the language. This invocation of an outside execution model is the defining characteristic of a programming model.

parallel programming models V.S. parallel languages
----------------------------------------------------------
The most obvious design goal for programming models is to support massive degrees of
parallelism，例如，GPU的数百个计算核心，在python runtime system是无法操作这些运算核心的。 It is impractical to make a new language for each execution model, hence it is a common practice to invoke the behaviors of the parallel execution model via an API. 