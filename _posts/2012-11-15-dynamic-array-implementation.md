---
title: "Dynamic Array Implementation in Java"
date: 2012-11-15
tags: [java, algorithm]
excerpt: "java, algorithm"
---

```java
package test.dynamic.array;

import java.util.*;

public class DynamicArray<T> {

    private static final int INIT_SIZE = 1;
    private T[] array; // the internal array
    private int size = 0;    // number of elements in dynamic array
    private static final double LOAD_FACTOR = 2.0; // Load factor used for array resizing

    public DynamicArray(){
        array = (T[]) new Object[INIT_SIZE];
    }

    // return the size of array
    public int size(){
        return size;
    }

    // is the array empty
    public boolean isEmpty(){
        return size() == 0;
    }

    // resize the array with capacity size
    private void resize(int capacity){
        T[] temp = (T[]) new Object[capacity];
        for(int i = 0; i < size; i++){
            temp[i] = array[i];
        }
        array = temp;
    }

    // add an element to the array
    public void add(T elem){
        if(size >= array.length){
            resize((int)LOAD_FACTOR * size);
        }
        array[size++] = elem;
    }

    // remove an element
    public void remove(T elem){
        int pos = find(elem);
        if(pos != -1){
            array[pos] = array[size - 1];
            array[size - 1] = null;
            size--;
        }

        int newsize = (int)(array.length / (LOAD_FACTOR));
        if(size <= newsize + 1){
            resize(newsize + 1);
        }
    }

    // find the element in the array and return it's position
    public int find(T elem){
        for(int i = 0; i < size; i++){
            if(array[i] == elem){
                return i;
            }
        }
        return -1;
    }

    public Iterable<T> elements() {
        Queue<T> queue = new LinkedList<T>();

        for (int i = 0; i < size; i++)
            queue.add(array[i]);
        return queue;
    }

    public void print(){
        for(int i = 0; i < size; i++){
            System.out.print(array[i] + " ");
        }
        System.out.print("array.length : " + array.length + " size is : " + size);
        System.out.println();
    }

    /**
     * @param args
     */
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        DynamicArray<Integer> da = new DynamicArray<Integer>();
        for(int i = 0; i < 10; i++){
            da.add(i);
        }
        da.print();

        da.remove(2);
        da.print();
        da.remove(4);
        da.print();
        da.remove(6);
        da.print();
        da.remove(8);
        da.print();
        da.remove(0);
        da.print();
    }

}

```
