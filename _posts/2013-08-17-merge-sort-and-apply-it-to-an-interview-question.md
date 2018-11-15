---
title: "Merge Sort and apply it to an interview question"
date: 2013-08-17
tags: [java, python, algorithm]
excerpt: "java, python, algorithm"
---

Merge Sort [link](https://en.wikipedia.org/wiki/Merge_sort) is very efficient in sorting a list of elements with O(nlgn) complexity. It is used with divide and conquer strategy.

A few days ago, I came to an interview question, the description of the question is below:

There are two collections, list1 and list2, each collection has more than 10000 elements.

Please implement a method in Java without calling the system API; this method should return

a collection which contains all the elements of list1 and list2 and has no duplicate elements.

  The meaning of this question is to get the union of two sets.There are two points should be paid attention, first the duplicated elements in list1 and list2.The second is the duplicated elements in list1 or list2. We can implement a hash table to solve this issue. I try to solve this problem with merge sort idea. First I just want to merge the two lists but if you don't consider the order of the two lists, this method can't eliminate all the duplicate element.

  Below is the first version code in Python:
```python
def merge_set(left, right):
    result = []
    i, j = 0, 0
    while i < len(left) and j < len(right):
        if left[i] != right[j]:
            result.append(left[i])
            result.append(right[j])
        else:
            result.append(left[i])

        i += 1
        j += 1  

    while i < len(left):
        result.append(left[i])
        i += 1
    while j < len(right):
        result.append(right[j])
        j += 1

    return result

def union_set(S):
    n = len(S)
    if n <= 1:
        return S
    # divide
    mid = int(len(S) / 2) #n // 2
    L1 = S[0:mid]
    L2 = S[mid:n]

    # conquer with recursion
    left = union_set(L1)
    right = union_set(L2)
    # merge resultx
    return merge_set(left, right)


if __name__ == '__main__':
    S = [10, 5, 5, 5, 7]
    #merge_sort(S)

    L = union_set(S)
    print L

```

It got problem when running the test, because the order of two lists.

Below is the code that can work fine with two lists, duplicated or no duplicated.
```python
def merge(left, right):
    result = []
    i, j = 0, 0
    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i])
            i += 1
        elif left[i] == right[j]:   # do some modification when they are equal
            result.append(left[i])
            i += 1
            j += 1
        else:
            result.append(right[j])
            j += 1

    result += left[i:]
    result += right[j:]

    return result

def mergeset(lst):
    if len(lst) <= 1:
        return lst
    mid = int(len(lst) / 2)
    left = mergeset(lst[:mid])
    right = mergeset(lst[mid:])
    return merge(left, right)

if __name__ == "__main__":
    #L = mergeset([10, 5, 5, 5, 7])
    lst1 = [10, 5, 5, 5, 7]
    lst2 = [10, 5, 8, 8, 9, 11, 11, 12, 12]
    lst = lst1 + lst2
    L = mergeset(lst)
    print L

#    A = ['Hi', 'Ah', 'Wo', 'Hi', 'Hi']
#    L = mergeset(A)
#    print L

```

Below is the merge sort with java and python code, I paste here just for a record.

```java
package hello.sorting;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class MergeSort<T> {

	// the list to be sorted
	private List<T> lst;

	public MergeSort() {
		//lst = new ArrayList<T>(n);
		//this.lst = lst;
	}

	public void init(List<T> lst) {
		this.lst = lst;
	}

	/**
	 * merge two lists
	 * @param left
	 * @param right
	 * @return
	 */
	@SuppressWarnings({ "unchecked", "rawtypes" })
	public List<T> merge(List<T> left, List<T> right) {
		List<T> result = new ArrayList<T>();
		int i = 0, j = 0;

		while(i < left.size() && j < right.size()) {

			if(((Comparable)left.get(i)).compareTo(((Comparable)right.get(j))) <= 0) {
				result.add(left.get(i));
				i += 1;
			}
			else {
				result.add(right.get(j));
				j += 1;
			}
		}

		while(i < left.size()) {
			result.add(left.get(i));
			i++;
		}

		while(j < right.size()) {
			result.add(right.get(j));
			j++;
		}

		return result;
	}

	/**
	 * merge sort with dividing and conquering
	 * @param lst
	 * @return
	 */
	public List<T> mergeSort(List<T> lst) {
		if(lst.size() <= 1) {
			return lst;
		}

		// divide
		int mid = (int)(lst.size() / 2);
		List<T> L = lst.subList(0, mid);
		List<T> R = lst.subList(mid, lst.size());

		// conquer with recursion
		List<T> left = mergeSort(L);
		List<T> right = mergeSort(R);

		// merge the left and right list
		return merge(left, right);
	}

	/**
	 * generate a random integer list less than 100
	 * @param n
	 * @return
	 */
	public List<Integer> generateRandom(int n) {
		List<Integer> list = new ArrayList<Integer>();
		Random r = new Random();

		for(int i = 0; i < n; i++) {
			list.add(r.nextInt() % 100);
		}

		return list;
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MergeSort<Integer> ms = new MergeSort<Integer>();
		List<Integer> lst = ms.generateRandom(10);
		for(int i = 0; i < lst.size(); i++) {
			System.out.print(" " + lst.get(i));
		}
		System.out.println();

		ms.init(lst);

		List<Integer> result = ms.mergeSort(lst);

		for(int i = 0; i < result.size(); i++) {
			System.out.print(" " + result.get(i));
		}
		System.out.println();
		System.out.println("Ha..");
	}

}


```

Python code:
```python
def merge(S1, S2, S):
    '''
    Merge two sorted python lists S1 and S2 into properly size list S.
    '''
    i = j = 0
    while i + j < len(S):
        if j == len(S2) or (i < len(S1) and S1[i] < S2[j]):
            S[i + j] = S1[i]
            i += 1
        else:
            S[i + j] = S2[j]
            j += 1


def merge_sort(S):
    '''
    Sort the elements of python list S using merge-sort algorithm
    '''
    n = len(S)
    if n < 2:
        return
    # divide
    mid = n // 2
    S1 = S[0:mid]
    S2 = S[mid:n]
    # conquer with recursion
    merge_sort(S1)
    merge_sort(S2)
    # merge result
    merge(S1, S2, S)

```
