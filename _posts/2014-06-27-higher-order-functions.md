---
title: "Higher order functions"
date: 2014-06-27
tags: [higher order, python, c]
excerpt: "higher order, python, c"
---

Higher order function is powerful concept, it takes one or more functions as an input(argument) and outputs a function as return. It can make some complex problem become easy. Many languages support this concept and 
also has some built-in functions like map and filter. They use these functions in a very well-defined way to accomplish specific behaviors. Higher-order functions allow to implement much more sophisticated behaviors and can use it to write much more interesting programs. Have a try first. 

In math higher-order functions are known as operators or functionals. Map a function to another function. Many functional languages has map function, it takes as arguments function f and a list of elements, and apply each element of the list to f. In C and Java, some sorting functions take comparison function as a parameter, like standard function qsort as an example.

Some python code of higher-order function for fun:

```python
'''
Created on Jun 27, 2014
@author: will
'''

# Higher order functions.

def double(val):
    return 2 * val

def square(val):
    return val ** 2

print double(3)
print square(3)

def twice(func, val):
    return func(func(val))

print twice(double, 3)
print twice(square, 3)

data = [1, 3, 6, 9, 18]
newdata = [double(item) for item in data]
print newdata

newdata2 = map(square ,data)
print newdata2

def even(val):
    if val % 2:
        return False
    else:
        return True

newdata3 = filter(even, data)
print newdata3

# Get the area of a curve, it is just approximate the result
def area(func, low, high, stepsize):
    total = 0.0
    loc = low
    while loc < high:
        total += func(loc) * stepsize
        loc += stepsize

    return total

def f(x):
    return x

def g(x):
    return x ** 2

def h(x):
    if x < 3:
        return x
    elif x < 7:
        return x ** 2
    else:
        return 7 * x - 4

print area(f, 0, 10, .01)
print area(g, 0, 10, .0001)
print area(h, 0, 10, .001)

```

C higher-order function to compute integral:

```c
#include <stdio.h>

double integral(double f(double x), double a, double b, int n)
{
    // Compute the integral of f() with the interval [a, b]
    double sum = 0, dt = (b - a) / n;
    int i;
    for (i = 0; i < n; i++)
    {
        sum += f(a + (i + .5) * dt);
    }
    return sum * dt;
}

double f(double x)
{
    return 3 * x * x;
}

int main()
{
    double integ = integral(f, 0, 1, 100);
    printf("The integral is: %g\n", integ);
    return 0;
}

```

Reference:
[http://en.wikipedia.org/wiki/Higher-order_function](http://en.wikipedia.org/wiki/Higher-order_function)
