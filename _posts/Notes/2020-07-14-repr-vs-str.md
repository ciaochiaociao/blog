---
title: __repr__ vs __str__
---

* `__str__`: readable (for users), (`str()`, which defaults to `repr()` if `__str__` is not implemented)
* `__repr__`: must be unambiguous (`repr()`)
	- for machine to read
	- for developers to read
	- goal: `eval(repr(c)) == c`
	- e.g. `MyClass(this=%r, that=%r)`
```
def __repr__(self):
		return '{self.__class__.__name__}({self.ids!r}, {self.members!r})'.format(self=self)
```

others:
 - repr/str of a list are **repr** of its members