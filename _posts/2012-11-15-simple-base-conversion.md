---
title: "Simple base conversion implementation use java"
date: 2012-11-27
tags: [java, algorithm]
excerpt: "java, algorithm"
---

The stack interface defination:
```java
package test.baseconversion;

public interface IStack<T> {

    public abstract void push(T value);

    public abstract T pop();

    public abstract T top();

    public abstract boolean isEmpty();

    public abstract boolean isFull();

}

```

A sequence stack implementaion:
```java
package test.baseconversion;

public class SeqStack<T> implements IStack<T> {
    private int maxSize;
    private T[] stackArray;
    private int top;

    @SuppressWarnings("unchecked")
    public SeqStack(int size){
        maxSize = size;
        stackArray = (T[])new Object[maxSize];
        top = -1;
    }

    /* (non-Javadoc)
     * @see test.baseconversion.IStack#push(T)
     */
    @Override
    public void push(T value){
        assert(!isFull());
        stackArray[++top] = value;
    }

    /* (non-Javadoc)
     * @see test.baseconversion.IStack#pop()
     */
    @Override
    public T pop(){
        assert(!isEmpty());
        return stackArray[top--];
    }

    /* (non-Javadoc)
     * @see test.baseconversion.IStack#top()
     */
    @Override
    public T top(){
        return stackArray[top];
    }

    /* (non-Javadoc)
     * @see test.baseconversion.IStack#isEmpty()
     */
    @Override
    public boolean isEmpty(){
        return (top == -1);
    }

    /* (non-Javadoc)
     * @see test.baseconversion.IStack#isFull()
     */
    @Override
    public boolean isFull(){
        return (top == maxSize - 1);
    }
}

```

The conversion implementation:
```java
package test.baseconversion;

public class BaseConversion {

    public String recursiveConversion(int number, int base){
        String result = "";
        if(number < base){
            result = new String("" + number);
        }
        else{
            result = recursiveConversion(number/base, base) + new String("" + number % base);
        }

        return result;
    }

    public String conversion(int number, int base){
        String result = "";
        SeqStack<Integer> s = new SeqStack<Integer>(20);

        while(number > 0){
            s.push(number % base);
            number = number / base;
        }

        while(!s.isEmpty()){
            result += s.pop();
        }

        return result;
    }
    /**
     * @param args
     */
    public static void main(String[] args) {
        // TODO Auto-generated method stub

        String result;
        BaseConversion baseconversion = new BaseConversion();
        result = baseconversion.recursiveConversion(10, 2);
        System.out.println("recursive result is : " + result);

        result = baseconversion.conversion(10, 2);
        System.out.println("non recursive result is : " + result);
    }

}

```
