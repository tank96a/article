---
title: Some python codes
author: tank96a
layout: post
categories:
  - article
tags:
  - python
---

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

{% raw %}
<pre>
root@kali:~/Desktop# openssl --help
root@kali:~/Desktop# openssl genrsa --help
root@kali:~/Desktop# openssl genrsa -out mykey.pem
Generating RSA private key, 1024 bit long modulus
e is 65537 (0x10001)
root@kali:~/Desktop# openssl rsa -help
root@kali:~/Desktop# openssl rsa -in mykey.pem -pubout > mykey.pub
</pre>
{% endraw %}

{% highlight python %}
from Crypto.PublicKey import RSA
text = "My test!"
pub_key = RSA.importKey(open('mykey.pub'))
# the second parameter for compatibility only,'' will be ok. Return a tuple with two items.
#The first item is the ciphertext,The second item is always None.
x = pub_key.encrypt(text, '')  

pri_key = RSA.importKey(open('mykey.pem'))
decrypted_text = pri_key.decrypt(x[0])
print decrypted_text
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}
