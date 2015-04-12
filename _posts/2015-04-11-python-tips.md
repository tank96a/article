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
--------------------------------------------------
import uuid
import hashlib
deskey = hashlib.md5(uuid.uuid1().hex).hexdigest()[:8]
print deskey
---------------------------------------------------

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
{% endhighlight %}

{% highlight python %}
{% endhighlight %}
