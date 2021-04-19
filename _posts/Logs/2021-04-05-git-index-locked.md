---
title: git index locked issue
---

ref: 

- https://thoughtbot.com/blog/how-to-fix-rm-f-git-index#:~:text=git%2Findex.,the%20file%20manually%20to%20continue.
- https://stackoverflow.com/questions/7860751/git-fatal-unable-to-create-path-my-project-git-index-lock-file-exists
- https://www.pluralsight.com/guides/understanding-and-using-git's-index.lock-file

cause: from my experience, when I use `dvc exp run --run-all --jobs` after `dvc exp --queue` with too many jobs running together and all of them stopped due to some of the bugs in my code, all fired up ipython debugger (because I use `ipython --pdb`). I closes some of those ipythons (with `Ctrl + D`), but couldn't shut all of them off. So I used `htop` to kill those processes. Then when I used some git commands, like `git switch` the error of `index locked` showed. 

solution: delete `.git/index.lock`

