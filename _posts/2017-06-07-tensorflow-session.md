---
layout: post
title:  "Tensorflow中Session是什么"
date:   2017-06-07 10:23:06 +0800
categories: Tensorflow
---

>翻译自：[What is a TensorFlow Session?](http://danijar.com/what-is-a-tensorflow-session/)

好多人搞不清楚tf.Graph 与tf.Session ，这很简单（原文是 It's simple :p ）：
* Graph定义了计算。 它不计算任何值，它不包含任何值，它是代码中定义的指定操作。
* Session允许执行Graph或Graph的一部分。 它分配资源（在一台或多台机器上），并保存中间结果和变量的实际值。
我们来看一个例子。

#定义Graph

我们定义一个带有变量和三个操作的图：变量总是返回我们变量的当前值。 initialize将初始值42赋给该变量。 assign将新值为13赋给该变量。
```python
graph = tf.Graph()
with graph.as_default():
    variable = tf.Variable(42, name='foo')
    initialize = tf.initialize_all_variables()
    assign = variable.assign(13)
```
注：Tensorflow会被帮我们创建一个默认的Graph，前两行的代码可以省略。


#在Session中运行计算

要运行刚才定义的三个操作中的任何一个，我们需要为Graph创建一个Session。 Session还将分配内存来存储变量的当前值。

```python
with tf.Session(graph=graph) as sess:
  sess.run(initialize)
  sess.run(assign)
  print(sess.run(variable))
# Output: 13
```
变量的值仅在一个会话中有效， 如果我们尝试在第二个会话中查询该值，TensorFlow将引发一个错误，因为该变量没有在那里初始化。

```python
with tf.Session(graph=graph) as sess:
  print(sess.run(variable))
# Error: Attempting to use uninitialized value foo
```

当然，我们可以在多个Session中使用图表，我们只需要重新初始化变量。 新的Session中的值将与第一个完全独立：

```python
with tf.Session(graph=graph) as sess:
  sess.run(initialize)
  print(sess.run(variable))
# Output: 42
```