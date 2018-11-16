---
title: "Find the kth largest element in an unsorted array"
date: 2016-03-11
tags: [java, algorithm]
excerpt: "java, algorithm"
---

This is should be an old problem, I had written some code related to this problem long time ago. This time I just write the code for review. The idea is the same as quick sort. There are many other methods too, but the divide and conquer way should be a better one.

No more words , just paste the code now.
```java
package leetcode.num_215;

import java.util.Random;

/*
 * Find the kth largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.
For example,
Given [3,2,1,5,6,4] and k = 2, return 5.
Note:
You may assume k is always valid, 1 ≤ k ≤ array's length.
 */
public class Solution {
	public int findKthLargest(int[] nums, int k) {
		int newK = nums.length - k + 1;

		return randomizedSelect(nums, 0, nums.length-1, newK);
	}

	public int randomizedSelect(int[] nums, int left, int right, int k) {
		if (k < 0 || k > nums.length) throw new IllegalArgumentException();

		int pivot = randomPartition(nums, left, right);

		if (left == right) return nums[left];

		int d = pivot - left + 1;

		if (k == d) {
			return nums[pivot];
		} else if (k < d) {
			return randomizedSelect(nums, left, pivot-1, k);
		} else {
			return randomizedSelect(nums, pivot+1, right, k-d);
		}
	}

	private int partition(int[] nums, int left, int right) {
		int x = nums[right];
		int i = left-1;

		for (int j = left; j < right; j++) {
			if (nums[j] < x) {
				i += 1;
				swap(nums, i, j);
			}
		}
		swap(nums, i+1, right);
		return i+1;
	}

	private void swap(int[] nums, int i, int j) {
		int temp = nums[i];
		nums[i] = nums[j];
		nums[j] = temp;
	}

	private int randomPartition(int[] nums, int left, int right) {
		Random random = new Random();
		int r = random.nextInt(right+1-left) + left; // This place needs to pay attention.
		swap(nums, r, right);

		return partition(nums, left, right);
	}

	public void randomQuickSort(int[] nums, int left, int right) {
		if (left < 0 || right > nums.length) return;

		if (left < right) {
			int q = randomPartition(nums, left, right);
			randomQuickSort(nums, left, q-1);
			randomQuickSort(nums, q+1, right);
		}
	}

	public void printNums(int[] nums) {
		for (int i = 0; i < nums.length; i++) {
			System.out.print(nums[i] + " ");
		}
		System.out.println();
	}

	public static void main(String[] args) {
		int[] nums = { 3, 2, 1, 5, 6, 4 };
		Solution s = new Solution();
		s.printNums(nums);
		s.randomQuickSort(nums, 0, nums.length-1);
		s.printNums(nums);

		int num = s.findKthLargest(nums, 2);
		System.out.println(num);

		int[] nums1 = { 1 };
		int num1 = s.findKthLargest(nums1, 1);
		System.out.println(num1);
	}
}

```
