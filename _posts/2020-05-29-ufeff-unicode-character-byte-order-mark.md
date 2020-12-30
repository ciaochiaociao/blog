---
title: "\\ufeff unicode character (byte order mark)"
---

'﻿   0.9786733984947205'
`\ufeff   0.9786733984947205`

## `\ufeff` 
https://stackoverflow.com/questions/17912307/u-ufeff-in-python-string
The Unicode character U+FEFF is the byte order mark, or BOM, and is used to tell the difference between big- and little-endian UTF-16 encoding. If you decode the web page using the right codec, Python will remove it for you.

solution: https://blog.csdn.net/xiazhipeng1000/article/details/79720391

1. open the file with encoding 'utf-8-sig'
2. `"\ufeff   0.9786733984947205".encode('utf-8').decode('utf-8-sig')`

Why it exists?

`'aaa'.encode('utf-8-sig').decode('utf-8')`