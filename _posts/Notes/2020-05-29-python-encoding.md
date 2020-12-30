---
title: Python encoding
---

```
io.TextIOWrapper(sys.stdin.buffer, encoding='utf-8')
# Python 3.7 and newer
sys.stdin.reconfigure(encoding='utf-8')
```

`export PYTHONIOENCODING=utf8`


```
e8 = u.encode('utf-8')        # encode without BOM
e8s = u.encode('utf-8-sig')   # encode with BOM
e16 = u.encode('utf-16')      # encode with BOM
e16le = u.encode('utf-16le')  # encode without BOM
e16be = u.encode('utf-16be')  # encode without BOM
```
"sig" in "utf-8-sig" is the abbreviation of "signature" (i.e. signature utf-8 file). Using utf-8-sig to read a file will treat BOM as file info. instead of a string.