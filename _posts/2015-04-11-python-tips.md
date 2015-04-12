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
#获取代码运行时间
from  timeit import Timer 
def test1():
    l = [i for i in range(1000)]
def test2():
    l = list(range(1000))
    
t1 = Timer("test1()", "from __main__ import test1")
print("comprehension ",t1.timeit(number=1000), "milliseconds")
t2 = Timer("test2()", "from __main__ import test2")
print("list range ",t2.timeit(number=1000), "milliseconds")
{% endhighlight %}

[Python and cryptography with pycrypto](http://blog.csdn.net/lxgwm2008/article/details/9287481)
{% raw %}
<pre>
RSA 运算
root@kali:~/Desktop# openssl --help
root@kali:~/Desktop# openssl genrsa --help
root@kali:~/Desktop# openssl genrsa -out mykey.pem
Generating RSA private key, 1024 bit long modulus
e is 65537 (0x10001)
root@kali:~/Desktop# openssl rsa -help
root@kali:~/Desktop# openssl rsa -in mykey.pem -pubout > mykey.pub

from Crypto.PublicKey import RSA
text = "My test!"
pub_key = RSA.importKey(open('mykey.pub'))
# the second parameter for compatibility only,'' will be ok. Return a tuple with two items.
#The first item is the ciphertext,The second item is always None.
x = pub_key.encrypt(text, '')  
pri_key = RSA.importKey(open('mykey.pem'))
decrypted_text = pri_key.decrypt(x[0])
print decrypted_text
--------------------------------------------------
from Crypto.PublicKey import RSA
from Crypto import Random
random_generator = Random.new().read
key = RSA.generate(1024, random_generator)
c=key.publickey().encrypt('Hello world!','')[0]
print key.decrypt(c)
pub_key =  key.publickey().exportKey()
pri_key = key.exportKey()
print pub_key+'\n\n'+pri_key
</pre>
{% endraw %}

{% highlight python %}
#DES运算
from Crypto.Cipher import DES
from Crypto import Random
text="My test!"
key='12345678' #It must be 8 byte long.
iv = Random.get_random_bytes(8)
des = DES.new(key,DES.MODE_CBC,iv)
reminder = len(text)%8
text+=chr(8-reminder)* (8-reminder)
c=des.encrypt(text)
des1 = DES.new(key,DES.MODE_CBC,iv)
p=des1.decrypt(c)
padNum = ord(p[-1])
print p[:-padNum]

#rc4运算
from Crypto.Cipher import ARC4
obj1 = ARC4.new('0CTF2015')
c= obj1.encrypt('0CTF{Backdoor&Obfuscation_In_Execution_Environment}')
print c.encode('hex') 
obj2 = ARC4.new('0CTF2015')
print obj2.decrypt(c)
{% endhighlight %}

{% highlight python %}
import uuid
import hashlib
deskey = hashlib.md5(uuid.uuid1().hex).hexdigest()[:8]

from Crypto.Hash import MD5
print MD5.new('admin888').hexdigest()
---------------------------------------------------
import base64
print base64.b64decode(base64.b64encode('123'))

import hashlib
s='test'.encode('rot13').encode('base64')
print s.decode('base64').decode('rot13')
----------------------------------------------------


{% endhighlight %}


{% highlight python %}
class Graph(object):
    def __init__(self, vertexNum=0, edges=0):
        self.EdgeNo = edges  # Edge counter.
        self.vertList =[[] for i in range(vertexNum)]  # Adjacency lists for each vertex.

    # Adds an edge between vertex number x and y
    def addEdge(self, x, y):  
        assert x != y
        self.vertList[x].append((y, self.EdgeNo))
        self.vertList[y].append((x, self.EdgeNo))
        self.EdgeNo += 1

    def printme(self):
        print len(self.vertList), self.EdgeNo
        for i in range(len(self.vertList)):
            for ref, cc in reversed(self.vertList[i]):
                if ref > i:
                    print i + 1, ref + 1

def toLineGraph(gra):  # Performs the line graph transform
    newGra = Graph(vertexNum=gra.EdgeNo)
    for vertex in gra.vertList:# for each vertex
        for i in range(len(vertex)):
            for e in range(i + 1, len(vertex)):
                # for each pair of its edges, make an edge between them
                newGra.addEdge(vertex[-i - 1][1], vertex[-e - 1][1])
    return newGra

def buildGraph(s):
    gra = Graph(len(s) + 2 + sum(ord(c) - ord('0') for c in s))
    for i in range(len(s) + 1):
        gra.addEdge(i, i + 1)
    lenxx = len(s) + 2
    for i in range(len(s)):
        for _ in range(ord(s[i]) - ord('0')):
            gra.addEdge(i + 1, lenxx)
            lenxx += 1
    return gra
    
toLineGraph(toLineGraph(buildGraph('123'))).printme()
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}
