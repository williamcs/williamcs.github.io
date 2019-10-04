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

Reference: [30 Helpful Python Snippets That You Can Learn in 30 Seconds or Less](https://towardsdatascience.com/30-helpful-python-snippets-that-you-can-learn-in-30-seconds-or-less-69bb49204172)