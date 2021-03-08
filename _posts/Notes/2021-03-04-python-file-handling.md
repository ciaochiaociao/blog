---
title: File Handling in Python
---

## read and write

https://stackoverflow.com/questions/6648493/how-to-open-a-file-for-both-reading-and-writing

```python
with open(filename, "r+") as f:
    data = f.read()
    f.seek(0)
    f.write(output)
    f.truncate()
```

> After you have read the file, the file pointer(fp) has moved forward, so you need to set it to the beginning. That's that `seek(0)` does: it places the fp to position `0` (*i.e.* the beginning). `truncate()` truncate the file to the provided number of bytes, *i.e.* removes all of the file content after the specified number of bytes. Imagine that your file has the string `Hello, world` and you write `Bye`. If you don't `truncate()` the content at the end will be `Byelo, world`, since you never deleted the text that existed in the file. `truncate()` truncates the file to the current fp

|          Mode          |  r   |  r+  |  w   |  w+  |  a   |  a+  |
| :--------------------: | :--: | :--: | :--: | :--: | :--: | :--: |
|          Read          |  +   |  +   |      |  +   |      |  +   |
|         Write          |      |  +   |  +   |  +   |  +   |  +   |
|         Create         |      |      |  +   |  +   |  +   |  +   |
|         Cover          |      |      |  +   |  +   |      |      |
| Point in the beginning |  +   |  +   |  +   |  +   |      |      |
|    Point in the end    |      |      |      |      |  +   |  +   |

![enter image description here](../../assets/images/t4k06.png)