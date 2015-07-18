---
title: some good examples
author: tank96a
layout: post
categories:
  - article
tags:
  - algo
---

1.The Word Ladder Problem

将pythonds-master.zip解压后放到安装目录中的lib目录下 C:\Python27\Lib\pythonds 就可以在脚本中直接使用了。

[python版的数据结构模块](https://github.com/bnmnetp/pythonds)
{% highlight python %}
from pythonds.graphs import Graph
from pythonds.basic import Queue

def traverse(y):
    path=''
    x = y
    while (x.getPred()):
        path+=x.getId()+'->'
        x = x.getPred()
    path+=x.getId()
    print path
    
def bfs(g, start):
    start.setDistance(0)
    start.setPred(None)
    vertQueue = Queue()
    vertQueue.enqueue(start)
    while (vertQueue.size() > 0):
        currentVert = vertQueue.dequeue()
        for nbr in currentVert.getConnections():
            if (nbr.getColor() == 'white'):
                nbr.setColor('gray')
                nbr.setDistance(currentVert.getDistance() + 1)
                nbr.setPred(currentVert)
                vertQueue.enqueue(nbr)
        currentVert.setColor('black')

def buildGraph(wordFile):
    d = {}
    g = Graph()
    wfile = open(wordFile,'r')
    # create buckets of words that differ by one letter
    for line in wfile:
        word = line[:-1]
        for i in range(len(word)):
            bucket = word[:i] + '_' + word[i+1:]
            if bucket in d:
                d[bucket].append(word)
            else:
                d[bucket] = [word]
    # add vertices and edges for words in the same bucket
    for bucket in d.keys():
        for word1 in d[bucket]:
            for word2 in d[bucket]:
                if word1 != word2:
                    g.addEdge(word1,word2)
    return g

g=buildGraph('wordfile.txt')
start=g.getVertex('fool')
bfs(g,start)
traverse(g.getVertex('sage'))  #sage->sale->pale->pall->poll->pool->fool
{% endhighlight %}
                
{% raw %}
<pre>
wordfile.txt 文件内容:
fail
foil
pall
pope
foul
pole
sale
fool
poll
pale
sage
cool
pool
page
</pre>
{% endraw %}

2.python 正则表达式

使用re.match  只是匹配开始
使用re.search 匹配任意位置，但在找到一个匹配项之后就会停止
使用 re.findall  返回一个列表

{% raw %}
<pre>
>>> rawString = r'and this is a\nraw string'
>>> print rawString
and this is a\nraw string
>>> match = re.search(r'dog', 'dog cat dog')
>>> match.start()
0
>>> match.end()
3
>>> re.findall(r'cat', 'dog cat dog')
['cat']
>>> contactInfo = 'Doe, John: 555-1212'
>>> match = re.search(r'(\w+), (\w+): (\S+)', contactInfo)
>>> match.group(1)
'Doe'
>>> match.group(2)
'John'
>>> match.group(3)
'555-1212'
>>> match = re.search(r'(?P<last>\w+), (?P<first>\w+): (?P<phone>\S+)', contactInfo)
>>> match.group('last')
'Doe'
>>> match.group('first')
'John'
>>> match.group('phone')
'555-1212'
>>> re.findall(r'(\w+), (\w+): (\S+)', contactInfo)
[('Doe', 'John', '!@#5()_+')]
>>> match=re.findall(r'(\w+), (\w+): (\S+)', contactInfo)
>>> match[0]
('Doe', 'John', '!@#5()_+')
>>> match[0][2]
'!@#5()_+'
</pre>
{% endraw %}










