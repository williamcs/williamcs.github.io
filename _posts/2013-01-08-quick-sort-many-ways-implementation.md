---
title: "QuickSort two way, three way partition and many other implementations"
date: 2013-01-08
tags: [c++, java, algorithm]
excerpt: "c++, java, algorithm"
---

Quicksort is so famous which was honored as one of the top 10 algorithms of 20th century in science and engineer. It is use the

divide and conquer strategy. Divde the array into two subarray and recursively sorting the subarray, then combine them together.

Most methods are inplace partition, so the extra memory is small. It is quick if the two sub recursion trees are approximately balanced.

So the pivot figure's choosing is the critical part for the performance. There are many ways to chose the pivot to partition like the median three parition. The duplicate elements is another issue should be considered, this leads to the three way partition algorithm. Quicksort is not stable 

and has too much overhead for tinny subarrays. It can be cutoff to insertion sort for items are less than 10.

Quicksort can also be implemented without recursion, so the stack would be used.  

Quicksort can also can be funny if you want to make fun.



Quicksort T shirt.

<img src="{{ site.url }}{{ site.baseurl }}/images/old/quick_sort.png" alt="linearly separable data">

Two way quicksort.
```java
package hello.test;

public class QuickSort {

	public void sort(Comparable[] a) {
		sort(a, 0, a.length - 1);
	}

	private void sort(Comparable[] a, int lo, int hi) {
		// TODO Auto-generated method stub
		if(lo >= hi) return;
		int piv = partition(a, lo, hi);
		sort(a, lo, piv - 1);
		sort(a, piv + 1, hi);
	}

	private int partition(Comparable[] a, int lo, int hi) {
		// TODO Auto-generated method stub
		int i = lo;
		int j = hi + 1;
		Comparable v = a[lo];
		while(true) {
			while(less(a[++i], v))
				if(i == hi) break;

			while(less(v, a[--j]))
				if(j == lo) break;

			if(i >= j) break;

			swap(a, i, j);
		}

		swap(a, lo, j);

		return j;
	}


	private void swap(Object[] a, int i, int j) {
		// TODO Auto-generated method stub
		Object tmp = a[i];
		a[i] = a[j];
		a[j] = tmp;
	}


	private boolean less(Comparable v, Comparable w) {
		// TODO Auto-generated method stub
		return (v.compareTo(w) < 0);
	}

	public void print(Comparable[] a) {
		for(int i = 0; i < a.length; i++) {
			System.out.print(a[i] + " ");
		}
		System.out.println();
	}


	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Integer[] a = {1, 5, 4, 3, 6};
		QuickSort qs = new QuickSort();
		qs.sort(a);
		qs.print(a);
	}

}

```

Three way quicksort

```java
package hello.test;

public class Quick3Way {

	public void sort(Comparable[] a) {
		sort(a, 0, a.length - 1);
	}

	private void sort(Comparable[] a, int lo, int hi) {
		if(hi <= lo) return;
		int lt = lo, gt = hi;
		Comparable v = a[lo];
		int i = lo;
		while(i <= gt) {
			int cmp = a[i].compareTo(v);
			if(cmp < 0) {
				swap(a, lt++, i++);
			} else if(cmp > 0) {
				swap(a, i, gt--);
			} else{
				i++;
			}
		}

		// a[lo..lt-1] < v = a[lt..gt] < a[gt + 1..hi].
		sort(a, lo, lt - 1);
		sort(a, gt + 1, hi);
	}

	private void swap(Object[] a, int i, int j) {
		// TODO Auto-generated method stub
		Object temp = a[i];
		a[i] = a[j];
		a[j] = temp;
	}

	public void print(Comparable[] a) {
		for(int i = 0; i < a.length; i++) {
			System.out.print(a[i] + " ");
		}
		System.out.println();
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Integer[] a = {1, 5, 4, 4, 5, 3, 6};
		Quick3Way q3w = new Quick3Way();
		q3w.sort(a);
		q3w.print(a);
	}

}

```

C++ implementation.
```cpp
#include <iostream>
#include <stdlib.h>
#include <time.h>
#include <assert.h>
#include <stack>

using namespace std;

typedef int FIELD;

const int DefaultSize = 100;

// List element struct definition
template<class Type> struct Element
{
	Type key;
	FIELD otherdata;
};

// Data List definition
template<class Type> class DataList
{
public:
	// constructor
	DataList(int maxSize = DefaultSize) : maxSize(maxSize), currentSize(0)
	{
		vector = new Element<Type>[maxSize];
	}

	// add
	void add(Type val)
	{
		if(currentSize < maxSize)
		{
			vector[currentSize++].key = val;
		}
	}

	// get ith element's key
	Type getKey(int i)
	{
		assert(i >= 0 && i <= currentSize);
		return vector[i].key;
	}

	// get ith elment
	Element<Type> get(int i)
	{
		/*if(i >= 0 && i <= currentSize)
		{
			return vector[i];
		}
		return NULL;*/

		assert(i >= 0 && i <= currentSize);
		return vector[i];
	}

	// set ith element's key to a new value val
	void setKey(int i, Type val)
	{
		if(i >= 0 && i <= currentSize)
		{
			return vector[i].key = val;
		}
	}

	// swap two element
	void swap(Element<Type> &x, Element<Type> &y)
	{
		Element<Type> temp = x;
		x = y;
		y = temp;
	}

	// swap elements in i and j of the list
	void swap(int i, int j)
	{
		Element<Type> temp = vector[i];
		vector[i] = vector[j];
		vector[j] = temp;
	}

	// get the median index between i, j and (i + j) / 2
	int median(int i, int j)
	{
		if(i >= 0 && i <= currentSize && j >= 0 && j <= currentSize)
		{
			int m = (i + j) / 2;
			Type first = vector[i].key;
			Type second = vector[m].key;
			Type third = vector[j].key;

			if((first - second) * (third - first ) >= 0)
			{
				return i;
			}
			else if((second - first) * (third - second) >= 0)
			{
				return m;
			}
			else
			{
				return j;
			}

		}
	}

	// print the list keys
	void show()
	{
		for(int i = 0; i < currentSize; i++)
		{
			cout << vector[i].key << " ";
		}
		cout << endl;
	}

private:
	Element<Type> *vector;	// the element list
	int maxSize;	// max size of the vector
	int currentSize;	// current size of the vector
};

// Quick Sort class definition
template<class Type> class QuickSort
{
public:
	void quickSort_left(DataList<Type> &list, const int left, const int right);
	void quickSort_right(DataList<Type> &list, const int left, const int right);
	void quickSort_mid(DataList<Type> &list, const int left, const int right);
	void quick3Sort(DataList<Type> &list, const int left, const int right);
	void quickSort(DataList<Type> &list, const int left, const int right);
	void quickSort_nonrec(DataList<Type> &list, int left, int right);
	void median3(DataList<Type> &list, const int low, const int high);
	void push2(int a, int b)	// inline function for pushing two numbers
	{
		qstack.push(b);
		qstack.push(a);
	}

private:
	stack<int> qstack;	// used for non recursive quicksort
	int partition_left(DataList<Type> &list, const int low, const int high);
	int partition_right(DataList<Type> &list, const int low, const int high);
	int partition_mid(DataList<Type> &list, const int low, const int high);
	int partition(DataList<Type> &list, const int low, const int high, const int pivotIndex);

};

// quick sort implementation, use leftmost element as pivot
template<class Type> void QuickSort<Type> :: quickSort_left(DataList<Type> &list, const int left, const int right)
{
	/*if(left < right)
	{
		int pivotpos = partition_left(list, left, right);
		quickSort_left(list, left, pivotpos - 1);
		quickSort_left(list, pivotpos + 1, right);
	}*/

	// this kind of partion is using right pointer outside the rightmost boundary
	// so when calling to sort, the right parameter should be list rightmost position + 1
	if(left + 1 < right)
	{
		Element<Type> pivot = list.get(left);
		int l = left + 1, r = right;
		while(l < r)
		{
			if(list.getKey(l) <= pivot.key)
			{
				l++;
			}
			else
			{
				list.swap(l, --r);
			}
		}
		list.swap(--l, left);
		quickSort_left(list, left, l);
		quickSort_left(list, r, right);
	}
}

// quick sort implementation, use rightmost element as pivot
template<class Type> void QuickSort<Type> :: quickSort_right(DataList<Type> &list, const int left, const int right)
{
	if(left < right)
	{
		int pivotpos = partition_right(list, left, right);
		quickSort_right(list, left, pivotpos - 1);
		quickSort_right(list, pivotpos + 1, right);
	}
}

// quick sort implementation, use middle element as pivot
template<class Type> void QuickSort<Type> :: quickSort_mid(DataList<Type> &list, const int left, const int right)
{
	/*if(left < right)
	{
		int pivotpos = partition_mid(list, left, right);
		quickSort_mid(list, left, pivotpos - 1);
		quickSort_mid(list, pivotpos + 1, right);
	}*/

	/******************************************************
	// use middle element as pivot and recurse directly.
	int i = left, j = right;
	Element<Type> pivot = list.get((left + right) / 2);
	while(i <= j)
	{
		while(list.getKey(i) < pivot.key)
		{
			i++;
		}
		while(list.getKey(j) > pivot.key)
		{
			j--;
		}
		if(i <= j)
		{
			list.swap(i, j);
			i++;
			j--;
		}
	}

	if(left < j)
	{
		quickSort_mid(list, left, j);
	}
	if(i < right)
	{
		quickSort_mid(list, i, right);
	}
	******************************************************/

	// partion the list and get the pivot position, a little difference with the previous sorting
	int pivotpos = partition_mid(list, left, right);
	if(left < pivotpos - 1)
	{
		quickSort_mid(list, left, pivotpos - 1);
	}
	if(pivotpos < right)
	{
		quickSort_mid(list, pivotpos, right);
	}
}

template<class Type> void QuickSort<Type> :: quickSort(DataList<Type> &list, const int left, const int right)
{
	if(left < right)
	{
		// the median three as pivot
		int pivotIndex = list.median(left, right);//(left + right) / 2;
		int pivotpos = partition(list, left, right, pivotIndex);
		quickSort(list, left, pivotpos - 1);
		quickSort(list, pivotpos + 1, right);
	}
}

/*******************************************************************************************************
// quicksort non recursive
The non-recursive version works by pushing the range of the sub-file to be sorted onto the stack.
Both right and left limits are pushed, one on top of the other. When they are popped, the list is
partitioned with those limits. This continues until there is no more subfile to be popped off the stack.
The program follows the policy of pushing the smaller subfileon top of the larger subfile.
Property if the smaller of the two subfiles is sorted first, then the stack never has more than lgN etries
when quicksort is used to sort N elements.
A lower size subfile on top ensures that the height of the stack will be lower than if the larger subfile
had been on top.
*******************************************************************************************************/
template<class Type> void QuickSort<Type> :: quickSort_nonrec(DataList<Type> &list, int left, int right)
{
	push2(left, right);
	while(!qstack.empty())
	{
		left = qstack.top(); qstack.pop();
		right = qstack.top(); qstack.pop();
		if(right <= left) continue;
		int i = partition_right(list, left, right);
		if(i - left > right - i)
		{
			push2(left, i - 1);
			push2(i + 1, right);
		}
		else
		{
			push2(i + 1, right);
			push2(left, i - 1);
		}
	}
}

// use the first elment as pivot to partition
template<class Type> int QuickSort<Type> :: partition_left(DataList<Type> &list, const int low, const int high)
{
	/*********************************************************************************
	int pivotpos = low;
	Element<Type> pivot = list.get(low);
	for(int i = low + 1; i <= high; i++)
	{
		if(list.getKey(i) < pivot.key)
		{
			list.swap(++pivotpos, i);
		}
	}
	list.swap(low, pivotpos);
	return pivotpos;
	*********************************************************************************/


	// use the leftmost element as pivot, left and right side scan simultaneously
	Element<Type> pivot = list.get(low);
	int i = low, j = high + 1;
	while(1)
	{
		while(list.getKey(++i) <= pivot.key && i <= high);
		while(list.getKey(--j) > pivot.key);

		if(i >= j) break;
		list.swap(i, j);
	}
	list.swap(low, j);
	return j;
}

// use last elment of list as pivot
template<class Type> int QuickSort<Type> :: partition_right(DataList<Type> &list, const int low, const int high)
{
	/*********************************************************************************
	// right
	int pivotpos = low;
	list.swap(low, high);
	Element<Type> pivot = list.get(low);
	for(int i = low + 1; i <= high; i++)
	{
		if(list.getKey(i) < pivot.key)
		{
			list.swap(++pivotpos, i);
		}
	}
	list.swap(low, pivotpos);
	return pivotpos;
	*********************************************************************************/

	/*********************************************************************************
	//right This partition method is similar to two color DNF
	int lo = low, hi = high;
	Element<Type> pivot = list.get(high);
	while(lo < hi)
	{
		if(list.getKey(lo) < pivot.key)
		{
			lo++;
		}
		else
		{
			hi--;
			list.swap(lo, hi);
		}
	}
	list.swap(hi, high);
	return hi;
	*********************************************************************************/

	/*********************************************************************************
	// right
	int i = low;
	int j = high - 1;
	Element<Type> pivot = list.get(high);
	for(; ;)
	{
		while(list.getKey(i) < pivot.key)
		{
			i++;
		}
		while(list.getKey(j) > pivot.key)
		{
			if(j == 0) break;
			j--;
		}
		if(i >= j)
		{
			break;
		}
		else
		{
			list.swap(i, j);
		}
	}
	list.swap(i, high);
	return i;
	*********************************************************************************/

	/*********************************************************************************
	// right
	Element<Type> pivot = list.get(high);
	int i = low - 1;
	int j = high;
	while(1)
	{
		while(list.getKey(++i) < pivot.key);
		while(j > 0 && list.getKey(--j) > pivot.key);
		if(i < j)
		{
			list.swap(i, j);
		}
		else
		{
			break;
		}
	}
	list.swap(i, high);
	return i;
	*********************************************************************************/

	Element<Type> pivot = list.get(high);
	int i = low - 1;
	int j = high;
	while(i < j)
	{
		while(list.getKey(++i) < pivot.key);
		while(j > 0 && list.getKey(--j) > pivot.key);
		if(i < j) list.swap(i, j);
	}
	list.swap(i, high);
	return i;
}

// use middle elment of list as pivot
template<class Type> int QuickSort<Type> :: partition_mid(DataList<Type> &list, const int low, const int high)
{
	/*********************************************************************************
	//right
	int pivotpos = low;
	int mid = (low + high) / 2;	// get the middle element position
	list.swap(low, mid);
	Element<Type> pivot = list.get(low);	// use the middle element as the pivot
	for(int i = low + 1; i <= high; i++)
	{
		if(list.getKey(i) < pivot.key)
		{
			list.swap(++pivotpos, i);
		}
	}
	list.swap(low, pivotpos);
	return pivotpos;
	********************************************************************************/

	/*********************************************************************************
	//another implement, left and right side simultaneously scan
	int mid = (low + high) / 2;
	list.swap(low, mid);
	Element<Type> pivot = list.get(low);
	int lo = low + 1, hi = high;
	while(lo <= hi)
	{
		while(list.getKey(hi) > pivot.key)
		{
			hi--;
		}
		while(lo <= hi && list.getKey(lo) <= pivot.key)
		{
			lo++;
		}
		if(lo <= hi)
		{
			list.swap(lo, hi);
			lo++;
			hi--;
		}
	}
	list.swap(low, hi);
	return hi;
	*********************************************************************************/

	/*********************************************************************************
	// right, use rightmost element as pivot and switch middle element to rightmost
	int mid = (low + high) / 2;
	list.swap(mid, high);
	Element<Type> pivot = list.get(high);

	int lo = low;
	int hi = high - 1;
	while(lo <= hi)
	{
		while(lo <= hi && list.getKey(lo) <= pivot.key)
		{
			lo++;
		}
		while(lo <= hi && list.getKey(hi) >= pivot.key)
		{
			hi--;
		}
		if(lo < hi)
		{
			list.swap(lo, hi);
		}
	}
	list.swap(high, lo);
	return lo;
	********************************************************************************/

	/*********************************************************************************
	// get problem with low + (low + high) / 2
	int pivotIndex = (low + high) / 2;
	Element<Type> pivot = list.get(pivotIndex);
	list.swap(pivotIndex, high);
	int left = low;
	int right = low;
	while(right < high)
	{
		if(pivot.key <= list.getKey(right))
		{
			right++;
		}
		else
		{
			list.swap(left, right);
			left++;
			right++;
		}
	}
	list.swap(left, high);
	return left;
	********************************************************************************/

	// use the middle element as the pivot, when sorting the pivot position should i
	// should be compared with left bound and right bound to decide the sub array's sorting
	int i = low, j = high;
	Element<Type> pivot = list.get((low + high) / 2);
	while(i <= j)
	{
		while(list.getKey(i) < pivot.key)
		{
			i++;
		}
		while(list.getKey(j) > pivot.key)
		{
			j--;
		}
		if(i <= j)
		{
			list.swap(i, j);
			i++;
			j--;
		}
	}
	return i;
}

// use pivotIndex to specify the pivot to do the partition
template<class Type> int QuickSort<Type> :: partition(DataList<Type> &list, const int low, const int high, const int pivotIndex)
{
	// This partition method is the same as Introduction to Algorithms' and the pivot position can be selected
	assert(pivotIndex >= low && pivotIndex <= high);
	list.swap(pivotIndex, high);	// move pivot to end
	Element<Type> pivot = list.get(high);
	int storeIndex = low - 1;

	for(int i = low; i < high; i++)
	{
		if(list.getKey(i) < pivot.key)
		{
			storeIndex++;
			list.swap(i, storeIndex);

		}
	}
	list.swap(high, storeIndex + 1);	// move pivot to its final place
	return storeIndex + 1;
}

// swap the list and make the median of left, center and right elements
template<class Type> void QuickSort<Type> :: median3(DataList<Type> &list, const int low, const int high)
{
	int mid = (low + high) / 2;
	if(list.getKey(mid) < list.getKey(low))
	{
		list.swap(mid, low);
	}
	if(list.getKey(high) < list.getKey(low))
	{
		list.swap(high, low);
	}
	if(list.getKey(high) < list.getKey(mid))
	{
		list.swap(high, mid);
	}
}

// 3 way quick sort implementation for the list with duplicated keys which follow the dutch natianal flag routine
template<class Type> void QuickSort<Type> :: quick3Sort(DataList<Type> &list, const int left, const int right)
{
	if(left < right)
	{
		int lt = left, gt = right;
		Element<Type> pivot = list.get(left);
		int i = left;
		while(i <= gt)
		{
			if(list.getKey(i) < pivot.key)
			{
				list.swap(lt++, i++);
			}
			else if(list.getKey(i) > pivot.key)
			{
				list.swap(i, gt--);
			}
			else
			{
				i++;
			}
		}

		// [left..lt - 1 | pivot = lt..gt | gt + 1..right]
		quick3Sort(list, left, lt - 1);
		quick3Sort(list, gt + 1, right);
	}
}

// generate the list for sorting.
DataList<int> generateList(int currentSize, int maxSize, int maxBound)
{
	srand(time(NULL));
	DataList<int> list(maxSize);// = new DataList<int>(maxSize);

	for(int i = 0; i < currentSize; i++)
	{
		int r = rand() % maxBound + 1;
		list.add(r);
	}

	return list;
}

int main()
{
	/*srand(time(NULL));
	int r = rand() % 10 + 1;
	cout << r << endl;*/

	DataList<int> list = generateList(10, 20, 100);
	list.show();

	/*quickSort1(list, 0, 10);
	list.show();*/

	QuickSort<int> qs;
	//qs.quickSort_left(list, 0, 9);
	//qs.median3(list, 0, 9);
	//list.show();
	qs.quickSort_mid(list, 0, 9);
	//qs.quickSort_right(list, 0, 9);
	//qs.quick3Sort(list, 0, 9);
	//qs.quickSort(list, 0, 9);

	//qs.quickSort_nonrec(list, 0, 9);
	list.show();

	return 0;
}


/*************************************************************************************
Two way partition algorithm:
Pick a pivot
Put the pivot out of the way(e.g swap it to the end of the array)
Do the Dutch flag routine on the rest of the array
Swap the pivot with leftmost blue element.
Adaption of the Dutch flag routine.
Red which is all the numbers less than the pivot
Blue whichi is all numbers greater than or equal to the pivot.
Just split int two parts(could have separated numbers which are equal to the
pivot - would correspond to white).
Loop invariant: everything to the left of the red counter is Red, everything
starting from the blue counter is Blue. Loop invariant is a property which holds
before the loop is executed and after each iteration through the loop.
red = l;	// set at the left border of the range
blue = r;	// set at the right border where the pivot sits
while(red < blue)
{
	if(arr[red] < pivot)
	{
		red++;
	}
	else
	{
		blue--;
		swap(arr, red, blue);
	}
	swap(arr, blue, r)	// put the pivot on the border
	return blue
}
Three way partition algorithm
 Let v be partitioning item a[lo].
 Scan i from left to right.
- a[i] less than v: exchange a[lt] with a[i] and increment both lt and i
- a[i] greater than v: exchange a[gt] with a[i] and decrement gt
- a[i] equal to v: increment i
It is similar to Three Colour Dutch National Flag problem
1. a[1..Lo-1] zeros(red)
2. a[Lo..Mid-1] ones(white)
3. a[Mid..Hi] unknown
4. a[Hi+1..N] twos(blue)
Lo = 1; Mid = 1; Hi = N;
while(Mid <= Hi)
	Invariant: a[1..Lo-1] = 0 and a[Lo..Mid-1] = 1 and a[Hi+1..N] = 2; a[Mid..Hi] are unknown
	case a[Mid]
		0: swap(a[Lo], a[Mid]); Lo++; Mid++
		1: Mid++;
		2: swap(a[Mid], a[Hi]); Hi--;
****************************************************************************************/


```
