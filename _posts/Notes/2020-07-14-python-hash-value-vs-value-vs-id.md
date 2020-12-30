---
title: Python Hash Value vs Value vs ID
---

ref:

-   [What is the default \_\_hash\_\_ in python? - Stack Overflow](https://stackoverflow.com/questions/11324271/what-is-the-default-hash-in-python)
-   [python - What's a correct and good way to implement \_\_hash\_\_()? - Stack Overflow](https://stackoverflow.com/questions/2909106/whats-a-correct-and-good-way-to-implement-hash)
-   [stanza/doc.py at master · stanfordnlp/stanza](https://github.com/stanfordnlp/stanza/blob/master/stanza/models/common/doc.py)
-   [Inconsistent equality and hashing - Python queries - Semmle](https://help.semmle.com/wiki/display/PYTHON/Inconsistent+equality+and+hashing)
-   [python - Printing a list of objects of user defined class - Stack Overflow](https://stackoverflow.com/questions/12933964/printing-a-list-of-objects-of-user-defined-class)
-   [python - Difference between \_\_str\_\_ and \_\_repr\_\_? - Stack Overflow](https://stackoverflow.com/questions/1436703/difference-between-str-and-repr)

## Three points
*  `__eq__` returns **value** (`==` )
 
* `__hash__` returns **hash value**, which defaults to **value** (`__eq__`) when it is *immutable value(s)* (unhashable if mutable value(s) like *list*)
 
* `id()` returns **identity** (**is** keyword, `a is b` means `id(a) == id(b)`)
 the return value of which depends on how the interpreter implements, e.g., memory address in CPython
 
 ## unhashable problem
 (ref: https://help.semmle.com/wiki/display/PYTHON/Inconsistent+equality+and+hashing)
 - A class can be made unhashable by setting its __hash__ attribute to None.
 - In Python 3, if you define a class-level equality method and omit a __hash__ method then the class is automatically marked as unhashable.
	When you define an __eq__ method for a class, remember to implement a __hash__ method or set __hash__ = None.
```
# for example
	def __eq__(self):
		return ...
  
	__hash__ = None  # make it clear that it is unhashable
	def __hash__(self):  # if you want it hashable, implement this method
    return hash(repr(self))
		return hash(self._attr1) ^ hash(self._attr2) ^ ...
```
        # make children class hashable. This trick is especially needed when __eq__ is overridden, making unhashable  ref: https://help.semmle.com/wiki/display/PYTHON/Inconsistent+equality+and+hashing
				
				
## Membership test operations
(ref: https://docs.python.org/3/reference/expressions.html#membership-test-details)

 For container types such as list, tuple, set, frozenset, dict, or collections.deque, the expression x in y is equivalent to any(x is e or x == e for e in y).

For the string and bytes types, x in y is True if and only if x is a substring of y. An equivalent test is y.find(x) != -1. Empty strings are always considered to be a substring of any other string, so "" in "abc" will return True.

For user-defined classes which define the __contains__() method, x in y returns True if y.__contains__(x) returns a true value, and False otherwise.

For user-defined classes which do not define __contains__() but do define __iter__(), x in y is True if some value z, for which the expression x is z or x == z is true, is produced while iterating over y. If an exception is raised during the iteration, it is as if in raised that exception.

Lastly, the old-style iteration protocol is tried: if a class defines __getitem__(), x in y is True if and only if there is a non-negative integer index i such that x is y[i] or x == y[i], and no lower integer index raises the IndexError exception. (If any other exception is raised, it is as if in raised that exception).