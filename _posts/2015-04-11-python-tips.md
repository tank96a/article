---
title: Some python codes
author: tank96a
layout: post
categories:
  - article
tags:
  - python
---

##python 笔记

一些有用的python代码片段

{% highlight python %}

from  timeit import Timer #获取代码运行时间

def test1():
    l = [i for i in range(1000)]
def test2():
    l = list(range(1000))
    
t1 = Timer("test1()", "from __main__ import test1")
print("comprehension ",t1.timeit(number=1000), "milliseconds")
t2 = Timer("test2()", "from __main__ import test2")
print("list range ",t2.timeit(number=1000), "milliseconds")
{% endhighlight %}





