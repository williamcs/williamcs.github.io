---
title: "Disjoint set and poj 1611"
date: 2012-11-30
tags: [java, algorithm]
excerpt: "java, algorithm"
---

```java
package hello.test;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class DisjointSet {

	private final static int DEFAULT_SIZE = 10;
	private int[] parent;  // set array
	private int size;  // the size of the set array

	// constructor
	public DisjointSet() {
		this(DEFAULT_SIZE);
	}

	// constructor
	public DisjointSet(int size) {
		this.size = size;  // set number
		parent = new int[size + 1];

		for(int i = 0; i <= size; i++) {
			parent[i] = -1;  // every element of the set is one element set
		}
	}

	// get the ith element's value
	public int getParentNumber(int i) {
		if(i >= 0 && i < this.size + 1)
			return parent[i];
		return -1;
	}

	// find the root of x
	public int find(int x) {
		if(parent[x] < 0) {
			return x;
		} else {
			return find(parent[x]);
		}
	}

	// find the root of i and compress the path
	public int collapsingFind(int i) {
		int j = 0;
		for(j = i; parent[j] > 0; j = parent[j]) {} // search the i's root j

		// compress the path from i to j, set all the node's root to j
		while(i != j) {
			int temp = parent[i];
			parent[i] = j;
			i = temp;
		}

		return j;  // return root
	}

	// union two set's roots
	public void union(int root1, int root2) {
		parent[root2] = root1;
	}

	// use root's number as rank to union two sets
	public int weightedUnion(int root1, int root2) {
		if(root1 == root2) return root1;

		int tmp = parent[root1] + parent[root2];  // the sum of two roots' node as the new root nodes
		if(parent[root2] < parent[root1]) {  // root2 has more nodes
			parent[root1] = root2;  // make root2 as the root of root1
			parent[root2] = tmp;  // new root nodes
			return root2;
		} else {  // root1 has more nodes
			parent[root2] = root1;  // make root1 as the root of root2
			parent[root1] = tmp;
			return root1;
		}
	}



	/**
	 * @param args
	 * @throws FileNotFoundException
	 * solve poj 1611 The Suspects
	 */
	public static void main(String[] args) throws FileNotFoundException {
		long startTime = System.currentTimeMillis();   
		// TODO Auto-generated method stub
		@SuppressWarnings("resource")
		Scanner fin = new Scanner(new File("/home/will/myworld/workspace/HelloProject/src/hello/test/poj1611.txt"));
		int r1, r2;  // r1, r2 are roots number

		while(fin.hasNext()) {
			int n = fin.nextInt();
			int m = fin.nextInt();

			if(n == 0 && m == 0) break;

			DisjointSet ds = new DisjointSet(n);

			for (int i = 0; i < m; i++) {
				int k = fin.nextInt();
				int x = fin.nextInt();
				r1 = ds.collapsingFind(x);

				for(int j = 0; j < k - 1; j++) {
					int y = fin.nextInt();
					r2 = ds.collapsingFind(y);
					r1 = ds.weightedUnion(r1, r2);  // r1 becomes the new root, the left node will be union to r1
				}
			}

			System.out.println(-ds.getParentNumber(ds.collapsingFind(0)));
		}

		long endTime = System.currentTimeMillis();
		System.out.println("The run time is : " + (endTime - startTime) + " ms");
	}

}

```

The origin date of the publish is: 2012-11-30
