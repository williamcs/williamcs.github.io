---
title: "Select Sort in scala"
date: 2015-08-17
tags: [scala, sorting]
excerpt: "scala, sorting"
---

Below I written a simple select sort in scala, just for recording something.

```scala
object SelectSort {
  /*
    Return the index of the minimum element in the given
    array, index range [left, right]
  */
  def indexMin(a: Array[Int], left: Int, right: Int): Int = {
    var minVal = a(left);
    var minIndex = left;

    for (i <- (left + 1) to right) {
      if (a(i) < minVal) {
        minVal = a(i);
        minIndex = i;
      }
    }

    return minIndex;
  }

  /*
    Swaps the array elements at index i and j
  */
  def swap(a: Array[Int], i: Int, j: Int) {
    val temp = a(i);
    a(i) = a(j);
    a(j) = temp;
  }

  /*
    Sort the given array using select sort
  */
  def selectSort(a: Array[Int]) {
    val right = a.length - 1;

    for (left <- 0 until right) {
      val min = indexMin(a, left, right);
      if (left != min) {
        swap(a, left, min)
      }
     }
  }

  /*
    Another one
  */
  def selectSort1(a: Array[Int]) {
    val N = a.length;

    for (i <- 0 until N - 1) {
      var min = i;

      for (j <- i + 1 until N) {
        if (a(j) < a(min)) {
          min = j;
        }
      }
      swap(a, i, min);
    }
  }

  /*
    Output the array
  */
  def output(a: Array[Int]) {
    for (v <- a) {
      print(v + " ");
    }
    println();
  }

  /*
    Get random Int number betwen from and until
  */
  def random(from: Int, until: Int): Int = {
    val size = until - from;
    return from + (math.random * size).toInt;
  }

  /*
    Get the specified random numbers
  */

  def getRandomNum(n: Int, from: Int, until: Int): Array[Int] = {
    val elements:Array[Int] = new Array[Int](n);

    for (i <- 0 until n) {
      elements(i) = random(from, until);
    }

    elements;
  }

  /*
    Randomly permutes the given array's elements
  */
  def permute(a: Array[Int]) {
    val n = a.length;

    for (i <- 0 until (n - 1)) {
      val j = random(i, n);
      if (i != j) {
        swap(a, i, j);
      }
    }
  }

  // Doing the test

  /*
  val n = 10;
  val elements:Array[Int] = new Array[Int](n);

  for (i <- 0 until n) {
    elements(i) = i + 1;
  }

  permute(elements);*/

  val elements = getRandomNum(10, 0, 100);


  print("Before select sort: ");
  output(elements);

  print("After select sort: ");
  selectSort1(elements);
  output(elements);
}

```
