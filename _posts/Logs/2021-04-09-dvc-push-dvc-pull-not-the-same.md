---
title: dvc push dvc pull not the same
---

Reproduction:

1. `dvc status`
2. `dvc commit -f`
3. `dvc push`
4. `cd ..`
5. `git clone <local_repo> <path>`
6. `cd <path>`
7. `dvc pull`
8. `dvc status`: not clean

Causes:

- In my repo, I have `libs/ner_error_analysis` that is git submodule. This is not pulled by default
- `globalner/.gitignore`: `/libs`
- `globalner/libs/nlu`: soft link 

Solution:

- remove `globalner/libs` once and for all