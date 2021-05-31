---
title: Python MultiProcessing
---

```python
# Multiprocessing/parallel/batch

import multiprocessing as mp

#init objects
pool = mp.Pool(cores)
jobs = []

#create jobs
with open("input.txt") as f:
    for line in f:
        jobs.append( pool.apply_async(process,(line)) )

#wait for all jobs to finish
for job in jobs:
    job.get()

#clean up
pool.close()
```

