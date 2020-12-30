---
title: 
---

## Issue-20082301 (Solved): No References added in `_final_pred_and_set_ref()` during `predict()` / `evaluate()`

```
# does not add refs (`num_workers` is set 5 by default)
eval_result, loss = recoverer.evaluate(corpus.test, mini_batch_size=1, embedding_storage_mode='none')
# solution: set `num_workers=0` to disable multi-processing loading the dataset (reason unknown)
eval_result, loss = recoverer.evaluate(corpus.test, mini_batch_size=1, embedding_storage_mode='none', num_workers=0)
```

## Issue-20082302: prediction on `corpus.train` OOM

- Possible causes: `loss` has been summed when `return_loss` is set `True` (by default) without using `item()` to obtain the pure python data, which is put inside cpu memory