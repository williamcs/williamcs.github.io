---
title: "Quick sort and the Kth number"
date: 2012-11-22
tags: [python, algorithm]
excerpt: "python, algorithm"
---

```python
import random

# partition list L use the rightest element as pivot
def partition(L, left, right):
    x = L[right]
    i = left - 1
    for j in range(left, right):
        if L[j] < x:
            i += 1
            L[i], L[j] = L[j], L[i]
    L[i + 1], L[right] = L[right], L[i + 1]
    return i + 1

# random partition
def random_partition(L, left, right):
    ''' get a pivot figure in list L
        the parameter left and right must be used in the random method
        because the random method is called recursively
    '''
    r = random.randrange(left,right)
    L[r], L[right] = L[right], L[r]

    return partition(L, left, right)

# random quick sort
def random_quicksort(L, left, right):
    if left < 0 or right > len(L):
        return
    if left < right:
        q = random_partition(L, left, right)
        #print q
        random_quicksort(L, left, q - 1)
        random_quicksort(L, q + 1, right)

# quick sort
def quicksort(L, left, right):
    if left < 0 or right > len(L):
        return
    if left < right:
        q = partition(L, left, right)
        #print q
        quicksort(L, left, q - 1)
        quicksort(L, q + 1, right)

# select the kth number of the list L
def selectKth(L, left, right, k):
    assert k > 0 and k < len(L), "k must less than the length of the list"
    if left == right:
        return L[left]
    pivot = random_partition(L, left, right)
    #pivot = partition(L, left, right)
    d = pivot - left + 1
    #print d
    if k == d: # the pivot value is the answer
        return L[pivot]
    elif k < d:
        return selectKth(L, left, pivot - 1, k)
    else:
        return selectKth(L, pivot + 1, right, k - d)

def main():
    L = [1, 4, 3, 6, 5, 8, 7]
    print 'The original list is: ', L
    kth = selectKth(L, 0, 6, 1)
    print 'The kth number is ', kth

    random_quicksort(L, 0, 6)
    print 'The sorted list is: ', L

if __name__ == '__main__':
    main()



```
