---
title: "30 Helpful Python Snippets"
date: 2019-10-04
tags: [python]
excerpt: "python"
---

### 1. All unique
The following method checks whether the given list has duplicate elements. It uses the property of set() which removes duplicate elements from the list.

```python
def all_unique(lst):
    return len(lst) == len(set(lst))

x = [1, 1, 2, 2, 3, 2, 3, 4, 5, 6]
y = [1, 2, 3, 4, 5]

result_x = all_unique(x) # False
result_y = all_unique(y) # True

print("result_x = ", result_x)
print("result_y = ", result_y)
```

Running result:
```
result_x =  False
result_y =  True
```

### 2. Anagrams
This method can be used to check if two strings are anagrams. An anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

```python
from collections import Counter

def anagram(first, second):
    return Counter(first) == Counter(second)

anagram("abcd3", "3abcd") # True
```

Running result:
```
True
```

### 3. Memory
This snippet can be used to check the memory usage of an object.

```python
import sys

variable = 30
print(sys.getsizeof(variable)) # 30
```

Running result:
```
30
```

### 4. Byte size
This method returns the length of a string in bytes.

```python
def byte_size(string):
    return (len(string.encode('utf-8')))

print(byte_size('😀')) # 4
print(byte_size('Hello World')) # 11 
```

Running result:
```
4
11
```

### 5. Print a string N times
This snippet can be used to print a string n times without having to use loops to do it.

```python
n = 2
s = "Programming"

print(s * n) # ProgrammingProgramming
```

Running result:
```
ProgrammingProgramming
```

### 6. Capitalize first letters
This snippet simply uses the method title() to capitalize first letters of every word in a string.

```python
s = "Programming is awesome"

print(s.title()) # Programming Is Awesome
```

Running result:
```
Programming Is Awesome
```

### 7. Chunk
This method chunks a list into smaller lists of a specified size.

```python
def chunk(lst, size):
    return [lst[i: i + size] for i in range(0, len(lst), size)]

lst = ["Coding", "is", "awesome", "you", "know"]
chunk(lst, 2)
```

Running result:
```
[['Coding', 'is'], ['awesome', 'you'], ['know']]
```

### 8. Compact
This method removes falsy values (False, None, 0 and “”) from a list by using filter().

```python
def compact(lst):
    return list(filter(bool, lst))

lst = [0, 1, False, 2, '', 3, 'a', 's', 34]
compact(lst)
```

Running result:
```
[1, 2, 3, 'a', 's', 34]
```

### 9. Count by
This snippet can be used to transpose a 2D array.

```python
array = [['a', 'b'], ['c', 'd'], ['e', 'f']]
transposed = zip(*array)

print(transposed) # [('a', 'c', 'e'), ('b', 'd', 'f')]

for row in transposed:
    print(row)
```

Running result:
```
<zip object at 0x10b659548>
('a', 'c', 'e')
('b', 'd', 'f')
```

### 10. Chained comparison
You can do multiple comparisons with all kinds of operators in a single line.

```python
a = 3

print(2 < a < 8) # True
print(1 == a < 2) # False
```

Running result:
```
True
False
```

### 11. Comma-separated
This snippet can be used to turn a list of strings into a single string with each element from the list separated by commas.

```python
hobbies = ["basketball", "football", "swimming"]

print("My hobbies are:") # My hobbies are:
print(", ".join(hobbies)) # basketball, football, swimming
```

Running result:
```
My hobbies are:
basketball, football, swimming
```

### 12. Get vowels
This method gets vowels (‘a’, ‘e’, ‘i’, ‘o’, ‘u’) found in a string.

```python
def get_vowels(string):
    return [each for each in string if each in 'aeiou']

print(get_vowels('foobar')) # ['o', 'o', 'a']
print(get_vowels('gym')) # []
```

Running result:
```
['o', 'o', 'a']
[]
```

### 13. Decapitalize
This method can be used to turn the first letter of the given string into lowercase.

```python
def decapitalizer(string):
    return string[:1].lower() + string[1:]

print(decapitalizer('FooBar')) # 'fooBar'
print(decapitalizer('TouchBar')) # 'touchBar'
```

Running result:
```
fooBar
touchBar
```

### 14. Flatten
The following methods flatten a potentially deep list using recursion.

```python
def spread(arg):
    ret = []
    for i in arg:
        if isinstance(i, list):
            ret.extend(i)
        else:
            ret.append(i)
    
    return ret

def deep_flatten(xs):
    flat_list = []
    
    [flat_list.extend(deep_flatten(x)) for x in xs] if isinstance(xs, list) else flat_list.append(xs)
    
    return flat_list

deep_flatten([1, [2], [[3], 4], 5]) # [1, 2, 3, 4, 5]
```

Running result:
```
[1, 2, 3, 4, 5]
```

### 15. Difference
This method finds the difference between two iterables by keeping only the values that are in the first one.

```python
def difference(a, b):
    set_a = set(a)
    set_b = set(b)
    comparison = set_a.difference(set_b)
    return list(comparison)

difference([1, 2, 3], [1, 2, 4]) # [3]
```

Running result:
```
[3]
```

### 16. Difference by
The following method returns the difference between two lists after applying a given function to each element of both lists.

```python
def difference_by(a, b, fn):
    b = set(map(fn, b))
    return [item for item in a if fn(item) not in b]

from math import floor

print(difference_by([2.1, 1.2], [2.3, 3.4], floor)) # [1.2]
print(difference_by([{ 'x': 2 }, { 'x': 1 }], [{ 'x': 1 }], lambda v : v['x'])) # [ { x: 2 } ]
```

Running result:
```
[1.2]
[{'x': 2}]
```

### 17. Chained function call
You can call multiple functions inside a single line.

```python
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

a, b = 4, 5
print((subtract if a > b else add)(a, b)) # 9
```

Running result:
```
9
```

### 18. Has duplicates
The following method checks whether a list has duplicate values by using the fact that set() contains only unique elements.

```python
def has_duplicates(lst):
    return len(lst) != len(set(lst))

x = [1, 2, 3, 4, 5, 5]
y = [1, 2, 3, 4, 5]

print(has_duplicates(x)) # True
print(has_duplicates(y)) # False
```

Running result:
```
True
False
```

### 19. Merge two dictionaries
The following method can be used to merge two dictionaries.

```python
def merge_two_dicts(a, b):
    c = a.copy() # make a copy of a
    c.update(b) # modify keys and values of a with the ones from b
    return c

a = {'x': 1, 'y': 2}
b = {'y': 3, 'z': 4}
print(merge_two_dicts(a, b)) # {'y': 3, 'x': 1, 'z': 4}

def merge_dictionaries(a, b)
   return {**a, **b}


a = { 'x': 1, 'y': 2}
b = { 'y': 3, 'z': 4}
print(merge_dictionaries(a, b)) # {'y': 3, 'x': 1, 'z': 4}
```

Running result:
```
{'x': 1, 'y': 3, 'z': 4}
```

### 20. Convert two lists into a dictionary
The following method can be used to convert two lists into a dictionary.

```python
def to_dictionary(keys, values):
    return dict(zip(keys, values))

keys = ["a", "b", "c"]
values = [2, 3, 4]
print(to_dictionary(keys, values)) # {'a': 2, 'b': 3, 'c': 4}
```

Running result:
```
{'a': 2, 'b': 3, 'c': 4}
```

### 21. Use enumerate
This snippet shows that you can use enumerate to get both the values and the indexes of lists.

```python
lst = ['a', 'b', 'c', 'd']

for index, element in enumerate(lst):
        print("Value", element, "Index ", index)
        
# Value a Index  0
# Value b Index  1
# Value c Index  2
# Value d Index  3
```

Running result:
```
Value a Index  0
Value b Index  1
Value c Index  2
Value d Index  3
```

### 22. Time spent
This snippet can be used to calculate the time it takes to execute a particular code.

```python
import time

start_time = time.time()

a = 1
b = 2
c = a + b
print(c) # 3

end_time = time.time()
total_time = end_time - start_time
print("Time: ", total_time)

# 3
# Time:  0.0002338886260986328
```

Running result:
```
3
Time:  0.0002338886260986328
```

### 23. Try else
You can have an else clause as part of a try/except block, which is executed if no exception is thrown.

```python
try:
    2 * 3
except TypeError:
    print("An Exception was raised")
else:
    print("Thank God, no exception were raised.")
    
# Thank God, no exception were raised.
```

Running result:
```
Thank God, no exception were raised.
```

### 24. Most frequent
This method returns the most frequent element that appears in a list.

```python
def most_frequent(lst):
    return max(set(lst), key = lst.count)

numbers = [1, 2, 1, 2, 3, 2, 1, 4, 2]
most_frequent(numbers)
```

Running result:
```
2
```

### 25. Palindrome
This method checks whether a given string is a palindrome.

```python
def palindrome(a):
    return a == a[::-1]

palindrome('mom')
```

Running result:
```
True
```

### 26. Calculator without if-else
The following snippet shows how you can write a simple calculator without the need to use if-else conditions.

```python
import operator

actions = {
    "+": operator.add,
    "-": operator.sub,
    "/": operator.truediv,
    "*": operator.mul,
    "**": pow
}

print(actions['-'](50, 25)) # 25
```

Running result:
```
25
```

### 27. Shuffle
This snippet can be used to randomize the order of the elements in a list. Note that shuffle works in place, and returns None.

```python
from random import shuffle

foo = [1, 2, 3, 4]
shuffle(foo)

print(foo) # [1, 2, 4, 3]
```

Running result:
```
[4, 1, 3, 2]
```

### 28. Spread
This method flattens a list similarly like [].concat(…arr) in JavaScript.

```python
def spread(arg):
    ret = []
    
    for i in arg:
        if isinstance(i, list):
            ret.extend(i)
        else:
            ret.append(i)
            
    return ret

spread([1, 2, 3, [4, 5, 6], [7], 8, 9]) # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Running result:
```
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 29. Swap values
A really quick way for swapping two variables without having to use an additional one.

```python
a, b = -1, 14
a, b = b, a

print(a) # 14
print(b) # -1
```

Running result:
```
14
-1
```

### 30. Get default value for missing keys
This snippet shows how you can get a default value in case a key you are looking for is not included in the dictionary.

```python
d = {'a': 1, 'b': 2}

print(d.get('c', 3)) # 3
```

Running result:
```
3
```

Reference: [30 Helpful Python Snippets That You Can Learn in 30 Seconds or Less](https://towardsdatascience.com/30-helpful-python-snippets-that-you-can-learn-in-30-seconds-or-less-69bb49204172)