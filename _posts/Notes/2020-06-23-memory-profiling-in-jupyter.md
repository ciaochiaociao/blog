---
title: Memory Profiling in Jupyter
---

```
!pip install memory_profiler

%load_ext memory_profiler
```
```
%%file mprun_demo.py

def test():
    import en_core_web_sm
    _a = []
    for i in range(10):
        nlp = en_core_web_sm.load()
        text = 'Asociația Suporter Club Oțelul Galați (Romanian pronunciation: [oˈt͡selul ɡaˈlat͡sʲ]; Galați Steel), commonly known as Oțelul Galați, or simply as Oțelul, is a Romanian football club based in the city of Galați, Galați County, currently playing in the Liga III.'
        doc = nlp(text)
        tokens = [tok.text for tok in doc]
        _a.append(tokens)

        text = ' '.join(tokens)
```

```
from mprun_demo import test
%mprun -f test test()
```