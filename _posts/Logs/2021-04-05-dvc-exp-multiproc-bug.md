---
title: DVC exp Multi-Processing Bug
---

## Description

### Reproduce

```yaml
# in dvc.yaml
stages:
  main:
    cmd: ipython --pdb main.py
```

```bash
dvc exp run --temp --name first_try
dvc exp run --queue
dvc exp run --queue
dvc exp run --queue
dvc exp run --queue
dvc exp run --run-all --jobs 4 main
# Error of CUDA ran out of memory
# ipython debugger (pdb) is activated
# the processes were hanged and can not be closed by Ctrl-D
kill -15 <PIDs>
dvc exp apply first_try
# ERROR: unexpected error - the index is locked; this might be due to a concurrent or crashed process
```

## Solution

ref: 

- https://stackoverflow.com/questions/7860751/git-fatal-unable-to-create-path-my-project-git-index-lock-file-exists
- https://www.pluralsight.com/guides/understanding-and-using-git's-index.lock-file

In this case, you can resolve the problem simply by removing the `index.lock` file manually via this command or a similar command on your operating system: `rm .git/index.lock`.

## [More Reading](https://www.pluralsight.com/guides/understanding-and-using-git's-index.lock-file)

**About `index.lock`**

A little known fact is that whenever you run a git process, git creates an `index.lock` file within the `.git` directory. For example, if you run the command `git add .` to stage all local changes within your current repository, git will create this `index.lock` file while the `git add` command is running:

```
.git/
    ...
    index.lock
```

Upon successful completion of the `git add` process, the `index.lock` file is removed. What this does is ensure that simultaneous changes to your local git repo do not occur, as this would cause your git repo to be in an indeterminate state. The `index.lock` file prevents changes to your local repository from happening from outside of the currently running git process so as to ensure multiple git processes are not altering or changing the same repository internals at the same time.

**Issue caused by concurrent git process**

```
fatal: Unable to create '.git/index.lock': File exists.
```

So it makes sense that the error above is essentially telling you, "Hey, a git process is running in this git repo—you will have to wait to run the command you are attempting to run." However, it may be that there is no git process running! Sometimes, for whatever reason, a git process will not end gracefully. There are a number of reasons why this would happen. 