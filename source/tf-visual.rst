TF Visualization
==================
Package
------------------
Package **summary**, `on github <https://github.com/tensorflow/tensorflow/tree/r1.6/tensorflow/python/summary>`_, provide a way to **export condensed information about a model**, which is then accessible in tools such as TensorBoard.

这个包用于TensorFlow Event Processing, and  is primarily being developed to support **TensorBoard**, but it can be used by anyone who wishes to analyze or visualize TensorFlow **events files**.

How to write event file
--------------------------
1. 定义可视化参数

其实，就是使用Summary Ops(`on tf-guide <https://www.tensorflow.org/api_guides/python/summary#Generation_of_Summaries>`_)向Graph的GraphKeys.SUMMARIES这个collection中添加需要可视化的operations and tensors

.. code-block:: python
	:linenos:

	tf.summary.scalar("cls_loss",cls_loss_op)#cls_loss
	tf.summary.scalar("bbox_loss",bbox_loss_op)#bbox_loss
	tf.summary.scalar("landmark_loss",landmark_loss_op)#landmark_loss
	tf.summary.scalar("cls_accuracy",accuracy_op)#cls_acc
	summary_op = tf.summary.merge_all()

2. Creates a FileWriter

The FileWriter class provides a mechanism to create an event file in a given directory.

注意：并非tf.event.FileWriter()或者tf.EventWriter()，和tf.TFRecordWriter()比较。

.. code-block:: python
	:linenos:
    
	writer = tf.summary.FileWriter(logs_dir,sess.graph)

3. Add data to summaries

在没有运行的时候这些操作是不会执行任何东西的，仅仅是定义了一下而已。

.. code-block:: python
	:linenos:

	_,_,summary = sess.run([summary_op])

4. Write event protocol buffers to event files

class FileWriter updates the file contents asynchronously. This allows a training program to call methods to add data to the file directly from the training loop, without slowing down training.

注意:add_summary仅仅是向FileWriter对象的缓存中存放event data。 而向disk上写数据是由FileWrite对象控制的。

.. code-block:: python
	:linenos:

	writer.add_summary(summary,global_step=step)

5. flush

.. code-block:: python
	:linenos:

	#Call this method to make sure that all pending events have been written to disk.
	writer.flush()