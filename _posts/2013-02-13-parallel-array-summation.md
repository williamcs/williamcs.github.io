---
title: "Parallel array summation"
date: 2013-02-13
tags: [java, c++, algorithm]
excerpt: "java, c++, algorithm"
---

C++ code:
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define NUM_THREADS 4

int N;
int *X;
int gSum[NUM_THREADS];
//pthread_mutex_t sum_mutex = PTHREAD_MUTEX_INITIALIZER;

// do sum
void *summation(void *pArg)
{
	int tNum = *((int *) pArg);
	int lSum = 0;
	int start, end;

	start = (N / NUM_THREADS) * tNum;
	printf("start is %d\n", start);
	end = (N / NUM_THREADS) * (tNum + 1);
	printf("end is %d\n", end);
	if(tNum == (NUM_THREADS - 1))
	{
		end = N;
	}

	for (int i = start; i < end; i++)
	{
		lSum += X[i];
		//printf("%d ", i);
	}

	//pthread_mutex_lock(&sum_mutex);
	gSum[tNum] = lSum;
	//printf("%d ", gSum[tNum]);
	//pthread_mutex_unlock(&sum_mutex);

	free(pArg);
}

// initialize array
void initializeArray(int N)
{
	X = new int[N];
	int i;
	for (i = 0; i < N; i++)
	{
		X[i] = i + 1;
		//printf("%d ", X[i]);
	}
}

int main(int argc, char* argv[])
{
	int j, sum = 0;
	pthread_t tHandles[NUM_THREADS];

	N = 5;

	initializeArray(N);
	for (j = 0; j < NUM_THREADS; j++)
	{
		int *threadNum = new(int);
		*threadNum = j;
		pthread_create(&tHandles[j], NULL, summation, (void *)threadNum);
	}

	for (j = 0; j < NUM_THREADS; j++)
	{
		pthread_join(tHandles[j], NULL);
		sum += gSum[j];
		//printf("%d\n", gSum[j]);
	}

	printf("The sum of array elements is %d\n", sum);
}

```

Java code:

```java
package hello.test;

import java.util.Random;

public class ThreadSum extends Thread {
	private final int R = 10000;	// the random number threshold
	private int NUM_THREADS = 4; // number of threads
	private int[] a; // array that to sum
	private int N;	// the number of the array
	private int start;
	private int end;
	private static long[] gSum; // global array to store array chunk sum
	private int ithread; // the ith thread number
	//private int sum;

	// constructor
	public ThreadSum(int N) {
		this.N = N;
		a = generateRand();
		gSum = new long[NUM_THREADS];
		for (int i = 0; i < gSum.length; i++) {
			gSum[i] = 0;
		}
	}

	// constructor
	public ThreadSum(int[] a, int start, int end, int ithread) {
		this.a = a;
		this.start = start;
		this.end = end;
		this.ithread = ithread;
	}

	// generate the random array
	private int[] generateRand() {
		// TODO Auto-generated method stub
		Random r = new Random();
		int[]a = new int[N];
		for(int i = 0; i < a.length; i++) {
			//a[i] = r.nextInt(R);
			a[i] = i + 1;
		}
		return a;
	}

	// sum all the elements of array with threads
	public void summation() {
		int start, end;

		ThreadSum[] ts = new ThreadSum[NUM_THREADS];
		for (int i = 0; i < NUM_THREADS; i++) {
			start = (N / NUM_THREADS) * i;
			end = (N / NUM_THREADS) * (i + 1);

			if(i == (NUM_THREADS - 1))
			{
				end = N;
			}

			ts[i] = new ThreadSum(a, start, end, i);
			ts[i].start();
		}

		for (int j = 0; j < NUM_THREADS; j++) {
			if(ts[j] != null) {
				try {
					ts[j].join();
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
	}

	// sum the array which index is from start to end
	private void summation(int start, int end) {
		long lSum = 0;
		for (int i = start; i < end; i++) {
			lSum += a[i];
		}
		//return lSum;
		gSum[ithread] = lSum;
	}

	// multithread override
	public void run(){
		summation(start, end);
	}

	// return globla array gSum which stored the sub sum of array chunks
	public long[] getgSum() {
		return gSum;
	}

	// return the number of threads
	public int getNumThreads() {
		return NUM_THREADS;
	}

	// get threaded sum
	public void getThreadSum() {
		long[] result = getgSum();
		long sum = 0;
		for (int i = 0; i < result.length; i++) {
			sum += result[i];
			System.out.println(" result[" + i + "]: " + result[i]);
		}
		System.out.println("The thread sum is : " + sum);
	}

	// get serial sum
	public void getSerialSum() {
		long sum = 0;
		for (int i = 0; i< a.length; i++) {
			sum += a[i];
		}
		System.out.println("The serial sum is : " + sum);
	}

	// test function
	public static void main(String[] args) {
		ThreadSum threadsum = new ThreadSum(100000);

		long startTime1 = System.nanoTime();
		threadsum.summation();
		threadsum.getThreadSum();
		long stopTime1 = System.nanoTime();
		System.out.println("threaded sum cost time1 is: " + (stopTime1 - startTime1));

		long startTime2 = System.nanoTime();
		threadsum.getSerialSum();
		long stopTime2 = System.nanoTime();
		System.out.println("threaded sum cost time1 is: " + (stopTime2 - startTime2));

	}

}

```

Java code 2:

```java
package hello.test;

import java.util.Random;

public class ThreadSum {
	private final int R = 10000;	// the random number threshold
	private int NUM_THREADS = 4; // number of threads
	private int[] a; // array that to sum
	private int N;	// the number of the array
	private int start; // start index of array chunk
	private int end; // end index of array chunk
	private static long[] gSum; // global array to store array chunk sum
	private int ithread; // the ith thread number

	// constructor
	public ThreadSum(int N) {
		this.N = N;
		a = generateRand();
		gSum = new long[NUM_THREADS];
		for (int i = 0; i < gSum.length; i++) {
			gSum[i] = 0;
		}
	}

	// constructor
	public ThreadSum(int[] a, int start, int end, int ithread) {
		this.a = a;
		this.start = start;
		this.end = end;
		this.ithread = ithread;
	}

	// generate the random array
	private int[] generateRand() {
		// TODO Auto-generated method stub
		Random r = new Random();
		int[]a = new int[N];
		for(int i = 0; i < a.length; i++) {
			a[i] = r.nextInt(R);
			//a[i] = i + 1;
		}
		return a;
	}

	// sum all the elements of array with threads
	public void summation() {
		int start, end;

		for (int i = 0; i < NUM_THREADS; i++) {
			start = (N / NUM_THREADS) * i;
			end = (N / NUM_THREADS) * (i + 1);

			if(i == (NUM_THREADS - 1))
			{
				end = N;
			}

			Thread thread = new Thread(new SumRunnable(a, start, end, i));
			thread.start();
			try {
				thread.join();
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

	// sum array chunk from index start to end
	private void summation(int start, int end) {
		int lSum = 0;
		for (int i = start; i < end; i++) {
			lSum += a[i];
		}

		//return lSum;
		gSum[ithread] = lSum;
	}

	// return globla array gSum which stored the sub sum of array chunks
	public long[] getgSum() {
		return gSum;
	}

	// get threaded sum
	public void getThreadSum() {
		long[] result = getgSum();
		long sum = 0;
		for (int i = 0; i < result.length; i++) {
			sum += result[i];
			System.out.println(" result[" + i + "]: " + result[i]);
		}
		System.out.println("The thread sum is : " + sum);
	}

	// get serial sum
	public void getSerialSum() {
		long sum = 0;
		for (int i = 0; i< a.length; i++) {
			sum += a[i];
		}
		System.out.println("The serial sum is : " + sum);
	}

	// inner class that implement runnable interface
	private class SumRunnable implements Runnable {
		private int[] array; // the array to be sum
		private int start;	// start index
		private int end; // end index
		private int ithread; // ith thread number

		// constructor
		public SumRunnable(int[] array, int start, int end, int ithread) {
			this.array = array;
			this.start = start;
			this.end = end;
			this.ithread = ithread;
		}

		// override run()
		public void run() {
			ThreadSum threadsum = new ThreadSum(array, start, end, ithread);
			threadsum.summation(start, end);
		}
	}

	// test function
	public static void main(String[] args) {
		ThreadSum threadsum = new ThreadSum(1000000);

		long startTime1 = System.nanoTime();
		threadsum.summation();
		threadsum.getThreadSum();
		long stopTime1 = System.nanoTime();
		System.out.println("threaded sum cost time1 is: " + (stopTime1 - startTime1));

		long startTime2 = System.nanoTime();
		threadsum.getSerialSum();
		long stopTime2 = System.nanoTime();
		System.out.println("threaded sum cost time1 is: " + (stopTime2 - startTime2));

	}
}


```

Conclusion:  
The serial array summation is much faster than threaded array summation with small array numbers. When the array number is much bigger,

The cost time is get closer and closer.
