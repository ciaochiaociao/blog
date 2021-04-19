---
title: KBERT code tracing
---

candidate commit of UER forked from: `3ef800`

https://github.com/dbiir/UER-py/blob/3ef800592ef7ae9698e25b06851673a552621e20/run_classifier.py

```bash
curl https://raw.githubusercontent.com/dbiir/UER-py/3ef800592ef7ae9698e25b06851673a552621e20/run_classifier.py > /tmp/run_classifier.py
diff -Z -y /tmp/run_classifier.py ~/workspace/COSQA/KBERT/run_kbert_cls.py  # -Z: ignore trailing spaces, -y: side-by-side
diff -Z -u /tmp/run_classifier.py ~/workspace/COSQA/KBERT/run_kbert_cls.py  # -u: unified
diff -Z -c /tmp/run_classifier.py ~/workspace/COSQA/KBERT/run_kbert_cls.py  # -c: context
```

After checking the diff file, this should be modified by Liu WeiJiu (Author of KBERT) from the commit `3ef800` of UER:

- `BertClassifier.forward()`
  - add `pos` to `embedding`
  - add `vm` to `encoder`
- `add_knowledge_worker()`
- `main()`
  - `batch_loader()`
  - `read_dataset()`
  - `evaluate()`

