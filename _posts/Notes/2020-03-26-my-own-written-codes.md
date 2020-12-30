---
title: My Own Written Codes
---

```
def cache(f):
    def wrapper(*args, **kwargs):
        query = args[0]
        if query in wrapper.cache:
            return wrapper.cache[query]
        else:
            res = f(*args, **kwargs)
            wrapper.cache.update({query: res})
            return res
    wrapper.cache = {}
    return wrapper

@cache
def xx(a):
    return a*2

@cache
def xx2(b):
    return b**2
		
xx(1)
xx.cache
xx(3)
xx.cache
xx2(2)
xx2(4)
```