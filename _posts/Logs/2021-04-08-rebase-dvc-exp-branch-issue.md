---
title: rebase dvc exp branch issue
---

reproduction:

1. `dvc exp run`
2. `dvc exp branch myexp`
3. `git switch myexp`
4.  `(myexp) $ git rebase develop` 
5. `git switch` to the rebased branch `myexp`

reproduction 2:

1. `dvc exp run`
2. `git checkout <exp-commit>`

issues:

- `warning: unable to rmdir 'libs/ner_error_analysis': Directory not empty` 
  - `libs/ner_error_analysis`

debug:

```bash
$ git show <exp-commit>
...

diff --git a/libs/ner_error_analysis b/libs/ner_error_analysis
deleted file mode 160000
index 3a1cd34..0000000
--- a/libs/ner_error_analysis
+++ /dev/null
@@ -1 +0,0 @@
-Subproject commit 3a1cd3428fd2b35f29107b788f93630b547c739f

...
```

=> the directory libs/ner_error_analysis is deleted
possible causes:

tldr: `dvc exp` does not work with `git submodule`

- `libs/ner_error_analysis` is a submodule defined in `.gitmodule` and run by `git submodule int & update`: `libs/ner_error_analysis` is a file saving the hash/pointer of that directory. `.git/info/exclude` excludes `/libs` while keeps `/libs/ner_error_analysis`
- when running `dvc exp run`, it did not see `.git/info/exclude` in its created temporary subfolder (`./.dvc/tmp`), making it includes changes in `libs/ner_error_analysis`, nor did it run `git submodule init & update`, making the folder empty. When it finishes `dvc exp run`, it commits all changes, including the **folder deletion**.

Solution:

- Add back the reference by `git add /libs/ner_error_analysis` and `git commit -m 'Add back the sumodule reference'`

Note: it seems that `dvc exp` also does not work with `soft linked file` created by `ln -s`