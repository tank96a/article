---
title: some good examples
author: tank96a
layout: post
categories:
  - article
tags:
  - algo
---

1. The Word Ladder Problem

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
