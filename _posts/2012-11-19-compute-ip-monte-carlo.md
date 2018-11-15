---
title: "Computing PI using Monte Carlo method"
date: 2012-11-19
tags: [python, algorithm]
excerpt: "python, algorithm"
---

```python
import math
import random

class Point():
    def __init__(self, x = 0, y = 0):
        self.x = x
        self.y = y

    def set_rand_range(self, rand_start, rand_end):
        self.rand_start = rand_start
        self.rand_end = rand_end

    def get_random(self):
        r = random.uniform(self.rand_start, self.rand_end)
        return r

    def set_x(self):
        self.x = self.get_random()

    def get_x(self):
        return self.x

    def set_y(self):
        self.y = self.get_random()

    def get_y(self):
        return self.y

def get_randpoint():
    p = Point()
    p.set_rand_range(0, 1)
    p.set_x()
    p.set_y()
    return p.get_x(), p.get_y()

def compute_pi(n):
    x, y = 0, 0
    count = 0

    for i in range(n):
        x, y = get_randpoint()
        if math.sqrt(x * x + y * y) <= 1:
            count += 1

    pi = 4.0 * count / n
    print pi



def main():
    L = [100, 1000, 10000, 100000, 1000000]
    for i in range(len(L)):
        compute_pi(L[i])

if __name__ == '__main__':
    main()
    print math.pi

```
