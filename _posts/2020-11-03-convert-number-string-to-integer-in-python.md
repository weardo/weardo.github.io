---
layout: post
title: Convert number string to integer in Python
date: 2020-11-03 22:10 +0530
---

Following python code converts any number string to numeric value.

**Code:**
```python
number_string="10,931"
print("Number String: %s" % number_string)
number_digits = [int(d) for d in number_string if d.isdigit()]

number = np.sum([digit*(10**exponent) for digit, exponent in 
                    zip( number_digits[::-1], range(len(number_digits)) )])

print (number)
```
**Output:**
```python
Number String: 10,193
10193
```