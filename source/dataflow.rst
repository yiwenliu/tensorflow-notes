Dataflow Programming Model
============================
What is Dataflow Programming
-----------------------------
models a program as a directed graph of the data flowing between operations

Why dataflow graph
-------------------
- parallel computing
- large-scale data processing

.. _4-dataflow:

Four Concepts about Dataflow
-----------------------------
`Details of four major concepts on Google Cloud Platform <https://cloud.google.com/dataflow/model/programming-model>`_

- Pipelines, 等同于tf中的subgraph
- PCollections, 等同于tf中的tensor
- Transforms, 等同于tf中的node
- I/O Sources and Sinks(input source and output sink)，等同于tf中的inputing and outputing data

此四者的关系如下图所示，可见Dataflow就是由一个一个pipeline组成的：

.. image:: img/pipeline.png

.. _pipeline-stage:

Look inside a pipeline
-------------------------
1. composition

从上一小节看出，a pipeline等同于tf中的subgraph，有四个组件：input, transform, pcollections, output。Each pipeline represents a single, potentially repeatable job, from start to finish, in the Dataflow service.

2. stages

pipeline的stages类似于tf中的collections，pipeline中的transforms are grouped into different stages and transforms of the same stage are executed in parallel.

3. connections between stages

在用QueueRunner实现的tf "input pipeline"中，stages之间由queue来连接。

4. direction

一个stage中的所有transform执行成功后，才执行下一个stage的transform

.. _pipeline-design:

Pipeline Design Principles
----------------------------
对照上一小节“Four Concepts about Dataflow”，详见 `Pipeline Design Principles on Google Cloudplatform <https://cloud.google.com/dataflow/pipelines/design-principles>`_, 请结合文中提及的四个基本问题来认识pipeline的五种形态。

Apache Beam
--------------
2016年2月谷歌高调宣布将Apache Beam（原名Google DataFlow）贡献给Apache基金会孵化，Apache Beam被认为是继MapReduce，GFS和BigQuery等之后，谷歌在大数据处理领域对开源社区的又一个非常大的贡献。

.. image:: img/beam.jpg