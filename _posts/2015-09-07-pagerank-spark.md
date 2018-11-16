---
title: "PageRank Spark implementation"
date: 2015-09-07
tags: [scala, python, big data]
excerpt: "scala, python, big data"
---

As you know, PageRank is very famous algorithm. For the detail of pagerank defination and implemenation, you can refer to [pagerank wikipedia](https://en.wikipedia.org/wiki/PageRank)

<img src="{{ site.url }}{{ site.baseurl }}/images/old/wiki_pagerank_diagram.png" alt="linearly separable data">


There are many implementations, I have written some programs that had implemented it before. This time I try to use spark.

 The basic idea is below:

Give pages ranks(or scores) based on links to them.

Links from many pages -> high rank

Links from high-rank pages -> high rank

Algorithm:

1. Start each page at rank of 1
2. On each iteration, have page p contribute rank_of_p / neighbors_of_p
3. Set each page's rank to 0.15 + 0.85 * contribs



The Spark Program seems easy and short.

<img src="{{ site.url }}{{ site.baseurl }}/images/old/pagerank.png" alt="linearly separable data">

scala code:
```scala
object PageRank {
  // get link matrix
  val mat = sc.textFile("/home/will/myspace/mydev/mytest/sparktest/scalaspark/data/mat2.txt")

  // get the links of every page node
  val links = mat.map(line => {
    val parts = line.split("\\s+")
    (parts(0), parts(1))
  }).distinct().groupByKey()

  // initialize each page node's rank to 1.0
  var ranks = links.mapValues(v => 1.0)

  // set the iteration time to 10
  val ITERATIONS = 10

  // compute the page rank of each page node
  for (i <- 0 until ITERATIONS) {
    val contributions = links.join(ranks).flatMap {
      case (pageId, (links, rank)) =>
        links.map(dest => (dest, rank / links.size))
    }

    ranks = contributions.reduceByKey((x, y) => x + y).mapValues(v => 0.15 + 0.85 * v)
  }

  val rank_array = ranks.take(10)

  // print the result
  for (i <- 0 until rank_array.size) {
    println(rank_array(i))
  }
}

```

python code:
```python
# In[1]:

filename1 = "/home/will/myspace/mydev/mytest/sparktest/scalaspark/data/mat2.txt"
# get link matrix
mat = sc.textFile(filename1, 4, 0)


# In[3]:

# because the matrix is small, so that can be collected
mat.collect()


# In[4]:

import re
def parseNeighbors(urls):
    """Parses a urls pair string into urls pair."""
    parts = re.split(r'\s+', urls)
    return parts[0], parts[1]


# In[5]:

def computeContribs(urls, rank):
    """Calculates URL contributions to the rank of other URLs."""
    num_urls = len(urls)
    for url in urls:
        yield (url, rank / num_urls)


# In[6]:

# get the links of every page node
links = mat.map(lambda urls: parseNeighbors(urls)).distinct().groupByKey().cache()

# below method the get links will lead out of memory error
#

#links = mat.map(lambda line: line.split(" ")).map(lambda l: (l[0], l[1])).distinct().groupByKey().cache()#.mapValues(list).collect()

#links = mat.map(lambda line: (line.split(" ")[0], line.split(" ")[1])).distinct().groupByKey().cache()


# In[7]:

links.count()


# In[8]:

# Loads all URLs with other URL(s) link to from input file and initialize ranks of them to one.
ranks = links.map(lambda (url, neighbors): (url, 1.0))


# In[12]:

# compute pagerank
from operator import add

ITERATIONS = 10

# Calculates and updates URL ranks continuously using PageRank algorithm.
for iteration in xrange(ITERATIONS):
    # Calculates URL contributions to the rank of other URLs.
    contribs = links.join(ranks).flatMap(lambda (url, (urls, rank)): computeContribs(urls, rank))
    # Re-calculates URL ranks based on neighbor contributions.
    ranks = contribs.reduceByKey(add).mapValues(lambda rank: rank * 0.85 + 0.15)


# In[13]:

ranks.count()


# In[14]:

# print the result
for (link, rank) in ranks.collect():
    print "%s has rank: %s." % (link, rank)


```

The running result is below:
```
0 has rank: 0.772702281464.
6 has rank: 0.56251510134.
1 has rank: 1.72864431597.
7 has rank: 0.56251510134.
2 has rank: 1.14027517155.
8 has rank: 0.59949206817.
3 has rank: 0.970068542695.
9 has rank: 1.45593564966.
4 has rank: 1.23778322511.
5 has rank: 0.970068542695.

```

The input file of graph nodes like below:

```
0 1
1 2
1 2
1 3
1 3
1 4
2 3
3 0
4 0
4 2
5 1
1 5
6 4
4 5
4 3
2 4
2 5
7 8
8 1
4 8
9 2
2 9
3 9
5 9
7 9
9 6
9 7
```
